# ADR-0006: Represent Real-Time Stop Events as Passing Times

## Status

Accepted

## Context

SCRA and Mobility MCP require a source-independent semantic representation
of real-time arrival and departure information at public transport stops.

ADR-0003 establishes `ServiceJourney` as the semantic representation of a
scheduled passenger-carrying public transport operation.

ADR-0004 establishes `DatedVehicleJourney` and `MonitoredVehicleJourney` as
the semantic concepts used to represent the operation of a journey on a
specific operating day and its real-time monitored state.

GTFS-Realtime provides stop-level operational information primarily through
`TripUpdate.stop_time_update`.

A `StopTimeUpdate` describes a real-time update for arrival and/or departure
events at a particular stop on a trip.

GTFS-Realtime permits stop time updates for both future events and past
events. A `StopTimeEvent` may contain:

- `delay`
- `time`
- `scheduled_time`
- `uncertainty`

The GTFS-Realtime specification describes `time` as an estimated or actual
event expressed as an absolute POSIX timestamp.

Consequently, the GTFS-Realtime message structure does not itself provide a
complete semantic distinction between predicted and observed passing events.

SCRA should therefore not preserve `StopTimeUpdate` as the canonical semantic
structure.

Instead, the information should be interpreted according to established
public transport domain concepts.

## Evidence from Transmodel

Transmodel defines several forms of PASSING TIME.

The passing times produced by the scheduling process for publication in a
timetable are represented as:

- `TIMETABLED PASSING TIME`

Passing times associated with a particular operating day include:

- `TARGET PASSING TIME`
- `ESTIMATED PASSING TIME`
- `OBSERVED PASSING TIME`

A `TARGET PASSING TIME` represents the latest official plan for a
`DATED VEHICLE JOURNEY` at a `POINT IN JOURNEY PATTERN`.

An `ESTIMATED PASSING TIME` represents forecast information calculated from
the latest available operational input for a monitored journey.

An `OBSERVED PASSING TIME` represents recorded passing information for a
monitored journey.

These concepts provide a semantic distinction that is not explicit in the
GTFS-Realtime `StopTimeUpdate` message structure.

## Decision

SCRA and Mobility MCP will represent real-time stop arrival and departure
information using passing-time semantics rather than exposing
`StopTimeUpdate` as a canonical entity type.

The preferred semantic concepts are:

- `EstimatedPassingTime`
- `ObservedPassingTime`

`EstimatedPassingTime` represents predicted arrival, departure, or passing
information calculated from the latest available operational information.

`ObservedPassingTime` represents an arrival, departure, or passing event that
has actually occurred and has been recorded by the source system.

The conceptual mapping is therefore:

    GTFS-Realtime
    TripUpdate
        |
        +-- stop_time_update[]
                |
                +-- future predicted event
                |       |
                |       v
                |   EstimatedPassingTime
                |
                +-- confirmed observed event
                        |
                        v
                    ObservedPassingTime

`StopTimeUpdate` is therefore treated as a source-format structure rather
than as a canonical SCRA entity type.

## Relationship to the Journey

A passing-time entity describes an event associated with a particular
operational journey.

Conceptually:

    MonitoredVehicleJourney
            |
            +---- passingTime ----> EstimatedPassingTime
            |
            +---- passingTime ----> ObservedPassingTime

The exact direction and NGSI-LD vocabulary of the relationship will be
defined separately.

The important semantic requirement is that a passing-time entity can be
associated with the operational journey to which the event belongs.

## Relationship to the Stop

GTFS-Realtime identifies the affected stop using `stop_sequence`,
`stop_id`, or both, subject to the rules of the GTFS-Realtime specification.

SCRA will resolve this source-level identifier to the semantic public
transport location model established by ADR-0002.

For the initial implementation, a passing-time entity may therefore be
associated with a `PublicTransportStop`.

Conceptually:

    EstimatedPassingTime
            |
            +---- journey ----> MonitoredVehicleJourney
            |
            +---- stop -------> PublicTransportStop

and:

    ObservedPassingTime
            |
            +---- journey ----> MonitoredVehicleJourney
            |
            +---- stop -------> PublicTransportStop

The source `stop_sequence` may also be retained where required to
disambiguate repeated visits to the same stop within a journey.

## Point in Journey Pattern

Transmodel defines passing times more precisely in relation to a
`POINT IN JOURNEY PATTERN`.

This is semantically important because a public transport journey may visit
the same physical or logical stop more than once.

Therefore, the long-term semantic model should support the more precise
structure:

    MonitoredVehicleJourney
            |
            v
    EstimatedPassingTime
            |
            v
    PointInJourneyPattern
            |
            v
    PublicTransportStop

The same principle applies to `ObservedPassingTime`.

However, SCRA does not require `PointInJourneyPattern` to be introduced in
the initial implementation.

Until the journey-pattern model is defined, the combination of:

- journey identity
- stop identity
- stop sequence

may be used to preserve sufficient operational identity.

This allows the model to remain compatible with a future introduction of
`JourneyPattern` and `PointInJourneyPattern` without reproducing the
GTFS-Realtime message hierarchy.

## Arrival and Departure

A passing-time entity may describe arrival information, departure
information, or both.

For an estimated passing time, relevant semantic properties may include:

- `expectedArrivalTime`
- `expectedDepartureTime`

For an observed passing time, relevant semantic properties may include:

- `actualArrivalTime`
- `actualDepartureTime`

The exact property vocabulary will be defined separately and should reuse
existing standard vocabulary where semantically compatible.

A source event containing only arrival information does not require an
artificial departure value, and vice versa.

## Delay

GTFS-Realtime permits a `StopTimeEvent` to express a prediction through
either an absolute `time`, a relative `delay`, or both.

SCRA should preserve the semantic distinction between:

- the predicted or observed event time; and
- deviation from the relevant scheduled or target time.

A GTFS-Realtime `delay` should therefore not replace the passing-time
concept itself.

It may be represented as an additional property associated with the
passing-time information where useful.

## Uncertainty

GTFS-Realtime `StopTimeEvent.uncertainty` describes uncertainty associated
with the event time or delay.

This information is operationally useful and should not be discarded.

Where the source provides uncertainty information, SCRA should preserve it
using an appropriate property.

The absence of uncertainty information must not be interpreted as zero
uncertainty.

This follows the GTFS-Realtime semantics, where omitted uncertainty means
that uncertainty is unknown.

## Distinguishing Estimated and Observed Events

GTFS-Realtime does not provide a dedicated type discriminator that always
distinguishes an estimated event from an actual event.

The `time` field may represent either an estimated or an actual event.

Therefore, a mapper must not classify an event as
`ObservedPassingTime` merely because its timestamp is earlier than the
current time.

Temporal position alone is insufficient evidence that the event was
actually observed.

An event should be mapped to `ObservedPassingTime` only when the source data
or source-specific semantics provide sufficient evidence that the event is
recorded as having occurred.

Otherwise, real-time stop information should remain represented as
`EstimatedPassingTime`, or its observation status should remain unresolved
where the implementation supports such a distinction.

This conservative rule prevents SCRA from introducing semantic certainty
that is not present in the source data.

## Source Independence

This decision intentionally separates source transport structures from the
canonical semantic model.

For GTFS-Realtime:

    StopTimeUpdate
            |
            v
    semantic interpretation
            |
            +----> EstimatedPassingTime
            |
            +----> ObservedPassingTime

Other source standards may map to the same semantic concepts without
passing through GTFS-specific structures.

For example:

    GTFS-Realtime ----+
                      |
    SIRI -------------+----> Passing Time semantics
                      |
    Provider API -----+

This supports interoperability across public transport information systems.

## Consequences

### Positive

- SCRA does not expose GTFS-Realtime message nesting as its semantic model.
- Real-time arrival and departure information uses established public
  transport concepts.
- Predictions and recorded observations can be distinguished semantically.
- The model can support GTFS-Realtime, SIRI, and other operational sources.
- The model remains compatible with future introduction of
  `JourneyPattern` and `PointInJourneyPattern`.
- AI systems can reason directly about expected and observed arrival and
  departure events rather than interpreting source-specific protobuf
  structures.

### Trade-offs

- Mapping GTFS-Realtime events to estimated versus observed passing times
  may require source-specific interpretation.
- `PointInJourneyPattern` is not yet part of the initial SCRA semantic
  model.
- Some GTFS-Realtime information such as uncertainty and delay requires
  explicit semantic properties in addition to the passing-time concept.

These trade-offs are preferable to making the canonical semantic model
dependent on the structure of GTFS-Realtime.

## Implementation Guidance

The current Mobility MCP reference implementation represents
`TripUpdate.stop_time_update` information inside the Trip context.

This implementation should eventually be refactored so that stop-level
real-time events can be represented according to the passing-time model
defined by this ADR.

The implementation should not assume that every GTFS-Realtime
`StopTimeUpdate` corresponds to an observed event.

Where the source only provides prediction semantics, the resulting semantic
representation should use `EstimatedPassingTime`.

Where reliable source semantics identify a recorded event, the resulting
semantic representation may use `ObservedPassingTime`.

The mapper should preserve sufficient source information for traceability,
including the source stop identity and stop sequence where available.

## References

- GTFS-Realtime Reference:
  https://gtfs.org/documentation/realtime/reference/

- GTFS-Realtime Trip Updates:
  https://gtfs.org/documentation/realtime/feed-entities/trip-updates/

- GTFS-Realtime Best Practices:
  https://gtfs.org/documentation/realtime/realtime-best-practices/

- Transmodel:
  https://www.transmodel-cen.eu/

- Transmodel Passenger Information Tutorial:
  https://transmodel-cen.eu/index.php/passenger-information-tutorial/

- Transmodel Estimated Passing Time:
  https://www.transmodel-cen.eu/model/EARoot/EA5/EA4/EA3/EA1448.htm

- Transmodel v6.2 Data Dictionary:
  https://transmodel-cen.eu/wp-content/uploads/2024/09/Transmodel-v6.2-Data-Dictionary.pdf

- ETSI NGSI-LD:
  https://cim.etsi.org/NGSI-LD/official/

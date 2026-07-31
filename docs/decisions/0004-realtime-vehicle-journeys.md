# ADR-0004: Model Real-Time Operations with Dated and Monitored Vehicle Journeys

## Status

Accepted

## Context

SCRA and Mobility MCP require a source-independent semantic representation
of real-time public transport operations.

ADR-0003 establishes `ServiceJourney` as the preferred semantic concept for
a scheduled passenger-carrying public transport operation.

GTFS-Realtime adds another layer of information.

It does not merely repeat the static GTFS Trip definition. It provides
information about a particular operational trip instance, the physical
vehicle serving that trip, the vehicle's observed position and progress,
and predicted or observed events at stops.

GTFS-Realtime represents these concerns primarily through:

- `TripDescriptor`
- `TripUpdate`
- `VehiclePosition`
- `VehicleDescriptor`
- `StopTimeUpdate`

These source-format messages SHALL NOT automatically become canonical SCRA
semantic entity types.

The semantic layer instead requires concepts representing:

1. the scheduled service journey;
2. that journey on a particular operating day;
3. the monitored execution of that journey;
4. the physical vehicle;
5. observations of that vehicle; and
6. estimated or observed passing events at stops.

Transmodel provides established concepts for these distinctions, including:

- SERVICE JOURNEY
- DATED VEHICLE JOURNEY
- MONITORED VEHICLE JOURNEY
- VEHICLE
- ESTIMATED PASSING TIME
- OBSERVED PASSING TIME

## Decision

SCRA and Mobility MCP SHALL distinguish the scheduled, dated, monitored,
and physical-vehicle aspects of a public transport operation.

The conceptual structure SHALL be:

    ServiceJourney
          |
          | scheduled journey
          v
    DatedVehicleJourney
          |
          | monitored as operating
          v
    MonitoredVehicleJourney
          |
          +-- operatedByVehicle --> Vehicle

`ServiceJourney` represents the scheduled passenger service as established
by ADR-0003.

`DatedVehicleJourney` represents the operational journey associated with a
specific Operating Day.

`MonitoredVehicleJourney` represents the journey as it is being monitored
during operation.

`Vehicle` represents the physical transport vehicle.

These concepts SHALL remain semantically distinct even when a source format
provides information about several of them in the same message.

## GTFS-Realtime TripDescriptor

A GTFS-Realtime `TripDescriptor` SHALL NOT itself be treated as a canonical
semantic entity type.

It provides information used to identify, correlate, or characterize the
operational journey to which real-time information applies.

Depending on the GTFS-Realtime schedule relationship and service model,
relevant information may include:

- `trip_id`
- `route_id`
- `direction_id`
- `start_date`
- `start_time`
- `schedule_relationship`

For scheduled service, `trip_id` normally provides the link back to the
corresponding GTFS static Trip and therefore to the semantic
`ServiceJourney`.

`start_date`, and where required `start_time`, provide additional
information needed to distinguish a particular operational instance.

Conceptually:

    GTFS Static Trip
           |
           v
    ServiceJourney

    GTFS-RT TripDescriptor
           |
           | identifies / correlates
           v
    DatedVehicleJourney

This mapping is semantic rather than structural.

The precise SCRA identity construction rule for `DatedVehicleJourney`
SHALL be specified separately.

## Schedule Relationships

GTFS-Realtime can describe operational trips whose relationship to the
static schedule differs.

Examples include scheduled, canceled, unscheduled, duplicated, new, and
replacement operations, subject to the definitions and maturity status of
the corresponding GTFS-Realtime values.

SCRA SHALL interpret these values as operational semantics.

The original GTFS-Realtime enumeration SHALL NOT automatically become the
canonical semantic model.

In particular, the semantic model SHALL permit an operational journey to:

- correspond to a scheduled `ServiceJourney`;
- represent a modified or replacement operation;
- represent an additional or duplicated operation;
- represent an unscheduled operation; or
- represent a canceled scheduled operation.

The detailed vocabulary for these relationships SHALL be defined
separately after individual GTFS-Realtime schedule relationships have been
verified.

## MonitoredVehicleJourney

A `MonitoredVehicleJourney` represents the monitored execution of a dated
public transport operation.

It SHALL NOT be identified with the GTFS-Realtime `VehiclePosition`
message itself.

A `VehiclePosition` is a source message carrying observations and
operational references.

Those observations MAY contribute to the state of a
`MonitoredVehicleJourney`.

Conceptually:

    DatedVehicleJourney
           |
           | monitored as operating
           v
    MonitoredVehicleJourney
           |
           +-- operatedByVehicle --> Vehicle
           |
           +-- current operational progress
           |
           +-- current stop context
           |
           +-- real-time passing information

This distinction allows the monitored operation to remain stable while
individual observations are updated over time.

## Vehicle

The physical vehicle SHALL remain a separate semantic entity.

GTFS-Realtime `VehicleDescriptor` identifies or describes the physical
vehicle serving a trip.

GTFS-Realtime `VehiclePosition` may contain both:

- information about the physical vehicle; and
- information about the trip currently being served.

These SHALL be separated during semantic mapping.

Conceptually:

    GTFS-RT VehiclePosition
             |
             +-- VehicleDescriptor
             |        |
             |        v
             |      Vehicle
             |
             +-- Position
             |        |
             |        v
             |   Vehicle observation
             |
             +-- TripDescriptor
                      |
                      v
              operational journey

The vehicle entity MAY carry current observed state such as:

- location
- bearing
- speed
- odometer
- occupancy
- observation time

where suitable semantic vocabulary exists.

The exact allocation of transient observations between the `Vehicle`
entity and dedicated observation entities is outside the scope of this ADR
and MAY be refined later.

## VehiclePosition

GTFS-Realtime `VehiclePosition` SHALL be treated as source observation
information rather than as a canonical semantic entity type.

It may provide information including:

- current geographic position;
- observation timestamp;
- current stop;
- current stop sequence;
- current vehicle stop status;
- congestion information;
- occupancy information;
- the physical vehicle; and
- the trip currently being served.

The mapping layer SHALL distribute these source fields to the appropriate
semantic entities and relationships.

For example:

    VehiclePosition.position
             |
             v
    Vehicle.location

and:

    VehiclePosition.vehicle
             |
             v
    Vehicle

and:

    VehiclePosition.trip
             |
             v
    Dated / Monitored Vehicle Journey

The exact property vocabulary SHALL be verified individually.

## TripUpdate

GTFS-Realtime `TripUpdate` SHALL NOT automatically become a canonical
`TripUpdate` semantic entity.

GTFS defines TripUpdate as a real-time update on the progress of a vehicle
along a trip.

A TripUpdate applies to an actual trip instance and may contain updates for
multiple stops.

SCRA SHALL interpret the information contained in a TripUpdate and map it
to the relevant operational semantic concepts.

Conceptually:

    GTFS-RT TripUpdate
             |
             +-- TripDescriptor
             |        |
             |        v
             |   operational journey
             |
             +-- VehicleDescriptor
             |        |
             |        v
             |      Vehicle
             |
             +-- StopTimeUpdate
                      |
                      v
             real-time passing information

This avoids exposing the GTFS-Realtime protobuf message structure as the
domain semantic model.

## Estimated and Observed Passing Times

GTFS-Realtime `StopTimeUpdate` provides real-time arrival and/or departure
information for a particular stop on a trip.

Transmodel provides established concepts for real-time passing information.

These include:

- ESTIMATED PASSING TIME
- OBSERVED PASSING TIME

SCRA SHOULD use these established semantic concepts as the basis for
representing predicted and observed stop-passing information.

Conceptually:

    GTFS-RT StopTimeUpdate
              |
              | semantic mapping
              v
       Passing-time information
              |
              +-- journey --> Dated / Monitored Vehicle Journey
              |
              +-- stop ----> PublicTransportStop
              |
              +-- expectedArrivalTime
              |
              +-- expectedDepartureTime

An `EstimatedPassingTime` represents a forecast derived from the latest
available operational information.

An `ObservedPassingTime` represents an event that has actually been
observed or recorded.

The final SCRA vocabulary and entity structure for passing-time information
SHALL be specified separately.

In particular, this ADR does not require every StopTimeUpdate to become a
standalone NGSI-LD Entity. Implementations MAY use an entity, property,
relationship, or other NGSI-LD-compatible structure depending on the final
passing-time model.

## Correlation of TripUpdate and VehiclePosition

GTFS-Realtime allows TripUpdate and VehiclePosition information to be
provided separately.

GTFS-Realtime best practices recommend consistent pairing of
TripDescriptor and VehicleDescriptor identifiers across these feeds.

SCRA SHALL therefore support semantic correlation of these independent
source messages.

For example:

    VehiclePosition
        trip_id = X
        vehicle_id = Y
             |
             |
             +----------------------+
                                    |
    TripUpdate                      |
        trip_id = X                 |
        vehicle_id = Y              |
             |                      |
             +----------------------+
                     |
                     v
          MonitoredVehicleJourney
                     |
                     +-- operatedByVehicle --> Vehicle Y

The correlation SHALL be based on semantic identity and source identifiers,
not on feed-message nesting.

## Planned, Dated, and Monitored Layers

The resulting semantic architecture is conceptually:

    PublicTransportRoute
             ^
             |
        ServiceJourney
             |
             | dated on
             v
    DatedVehicleJourney
             |
             | monitored as operating
             v
    MonitoredVehicleJourney
             |
             +-- operatedByVehicle --> Vehicle
             |
             +-- current operational state
             |
             +-- passing-time information
                        |
                        +--> PublicTransportStop

This architecture separates long-term schedule semantics from operational
identity and real-time observation.

## Source Messages Are Not Domain Entities

A fundamental rule of this ADR is:

    GTFS-Realtime message type
              !=
    canonical SCRA entity type

`TripDescriptor`, `TripUpdate`, `VehiclePosition`, and `StopTimeUpdate`
are source-format structures.

They MAY contain information relevant to one or more semantic entities.

The adapter and semantic mapping layers SHALL interpret those structures
and distribute their information into the semantic graph.

This follows the same principle established for GTFS Route, Stop, and Trip
mapping in previous ADRs.

## Vocabulary Reuse

SCRA SHALL continue to follow the established vocabulary reuse policy:

1. NGSI-LD core vocabulary
2. suitable existing Smart Data Models vocabulary
3. suitable concepts from established domain standards such as
   Transmodel / NeTEx
4. other suitable established vocabularies
5. SCRA-defined vocabulary only where an adequate directly reusable term
   is not available

Transmodel provides the semantic basis for `DatedVehicleJourney`,
`MonitoredVehicleJourney`, and passing-time concepts.

Where no suitable stable official RDF or JSON-LD vocabulary IRI is
available for direct NGSI-LD use, SCRA MAY provide stable vocabulary IRIs.

For example, subject to final vocabulary verification:

    https://scra-schema.org/vocab/DatedVehicleJourney
    https://scra-schema.org/vocab/MonitoredVehicleJourney
    https://scra-schema.org/vocab/EstimatedPassingTime
    https://scra-schema.org/vocab/ObservedPassingTime

Such IRIs SHALL identify their external semantic basis and SHALL NOT imply
that SCRA invented the underlying transport-domain concepts.

## Rationale

GTFS-Realtime is optimized as a compact real-time exchange format.

Its protobuf message structure is not intended to be a general semantic
domain model.

Directly exposing TripUpdate or VehiclePosition as the canonical semantic
structure would couple SCRA to GTFS-Realtime and would make integration
with other real-time public transport standards more difficult.

Transmodel already distinguishes scheduled journeys, dated journeys,
monitored journeys, vehicles, and passing-time information.

Using these distinctions allows information from GTFS-Realtime and other
operational systems to converge on a common semantic representation.

Conceptually:

    GTFS-Realtime -----------+
                             |
    SIRI --------------------+--> Dated / Monitored
                             |    Vehicle Journey semantics
    Operator AVL system -----+
                             |
    Other real-time API -----+

This is consistent with the SCRA objective of semantic interoperability
rather than source-format replication.

## Consequences

- `TripDescriptor` SHALL NOT become a canonical SCRA entity merely because
  it exists in GTFS-Realtime.
- `TripUpdate` SHALL NOT automatically become a canonical `TripUpdate`
  entity.
- `VehiclePosition` SHALL NOT become a canonical `VehiclePosition` entity.
- `StopTimeUpdate` SHALL be semantically interpreted rather than copied as
  a nested GTFS-Realtime structure.
- `ServiceJourney`, `DatedVehicleJourney`, and
  `MonitoredVehicleJourney` SHALL remain distinct concepts.
- The physical `Vehicle` SHALL remain distinct from the journey it operates.
- Vehicle observations MAY update semantic Vehicle state without changing
  Vehicle identity.
- TripUpdate and VehiclePosition information MAY contribute to the same
  MonitoredVehicleJourney.
- Stop-time predictions SHOULD use established passing-time semantics.
- Source identifiers SHALL be usable for correlation while remaining
  separate from canonical semantic meaning.
- Real-time information SHALL augment rather than redefine the underlying
  scheduled semantic model.
- The resulting model SHALL remain usable with real-time sources other
  than GTFS-Realtime.

## Evidence

### GTFS-Realtime TripUpdate

The GTFS-Realtime Reference defines TripUpdate as a real-time update on the
progress of a vehicle along a trip.

A TripUpdate applies to an actual trip instance and contains a required
TripDescriptor.

GTFS-Realtime specifies that there can be at most one TripUpdate entity for
each actual trip instance.

StopTimeUpdate elements provide arrival and/or departure updates for
particular stops.

### GTFS-Realtime VehiclePosition

GTFS-Realtime defines VehiclePosition as real-time positioning information
for a given vehicle.

A VehiclePosition may identify the trip currently being served through a
TripDescriptor and may identify the physical vehicle through a
VehicleDescriptor.

It may also provide current position, current stop, stop sequence, status,
and other observed operational information.

### GTFS-Realtime Correlation

GTFS-Realtime best practices recommend that TripDescriptor and
VehicleDescriptor identifier pairings match between separate
VehiclePosition and TripUpdate feeds.

This supports treating the two message types as independent observations
about the same operational journey and vehicle.

### Transmodel Dated Vehicle Journey

Transmodel distinguishes a DATED VEHICLE JOURNEY associated with a
particular Operating Day.

The model relates a Dated Vehicle Journey to a Monitored Vehicle Journey
through the concept of being monitored as operating.

### Transmodel Monitored Vehicle Journey

Transmodel defines MONITORED VEHICLE JOURNEY as the monitored operational
representation of a vehicle journey.

The model relates the monitored journey to the vehicle operating it and to
vehicle-monitoring information.

### Transmodel Passing Times

Transmodel distinguishes several forms of passing-time information.

ESTIMATED PASSING TIME represents forecast information calculated from the
latest available operational input.

OBSERVED PASSING TIME represents recorded passing information.

These concepts provide an established semantic basis for interpreting
GTFS-Realtime StopTimeUpdate information.

## References

- GTFS-Realtime Reference:
  https://gtfs.org/documentation/realtime/reference/

- GTFS-Realtime Vehicle Positions:
  https://gtfs.org/documentation/realtime/feed-entities/vehicle-positions/

- GTFS-Realtime Trip Updates:
  https://gtfs.org/documentation/realtime/feed-entities/trip-updates/

- GTFS-Realtime Best Practices:
  https://gtfs.org/documentation/realtime/realtime-best-practices/

- Transmodel:
  https://www.transmodel-cen.eu/

- Transmodel Dated Vehicle Journey:
  https://www.transmodel-cen.eu/model/EARoot/EA3/EA3/EA937.htm

- Transmodel Passenger Information Tutorial:
  https://transmodel-cen.eu/index.php/passenger-information-tutorial/

- Transmodel v6.2 Data Dictionary:
  https://transmodel-cen.eu/wp-content/uploads/2024/09/Transmodel-v6.2-Data-Dictionary.pdf

# GTFS-Realtime Mapping

## Status

Normative

---

## 1. Purpose

This document defines the semantic correspondence between the General
Transit Feed Specification Realtime data model, referred to here as
GTFS-Realtime, and the canonical semantic models defined by SCRA Schema.

The purpose of this specification is to preserve the operational meaning of
GTFS-Realtime information while making it available through a
source-independent semantic model.

This specification does not define:

- an NGSI-LD representation;
- a JSON-LD serialization;
- an MCP interface;
- a software implementation;
- an ingestion pipeline; or
- a transport-data storage architecture.

---

## 2. Scope

This specification covers the GTFS-Realtime concepts required for the initial
SCRA public transport profile and Mobility MCP proof of concept.

The primary source concepts covered are:

- `FeedMessage`;
- `FeedHeader`;
- `FeedEntity`;
- `TripUpdate`;
- `TripDescriptor`;
- `VehiclePosition`;
- `VehicleDescriptor`;
- `Position`;
- `StopTimeUpdate`; and
- `StopTimeEvent`.

GTFS-Realtime Alerts, dynamic Shapes, dynamic Stops, and TripModifications
are recognised but are not normatively mapped by this version.

---

## 3. Mapping Principles

### 3.1 Semantic Correspondence

GTFS-Realtime structures shall be interpreted according to their source
semantics and mapped to corresponding SCRA semantic concepts.

Source message nesting shall not be reproduced as the canonical semantic
model.

A single GTFS-Realtime entity may contribute information to multiple SCRA
semantic instances.

A single SCRA semantic instance may be updated from multiple GTFS-Realtime
entities or feeds.

---

### 3.2 Relationship to GTFS Static

A GTFS-Realtime feed is interpreted in relation to an applicable GTFS Static
dataset.

Identifiers such as:

- `trip_id`;
- `route_id`;
- `stop_id`; and
- `start_date`

shall be resolved within the appropriate GTFS source and service context.

A realtime mapping shall not assume that source identifiers are globally
unique.

---

### 3.3 Separation of Identity and State

Source identifiers may contribute to semantic identity resolution.

Operational values such as:

- location;
- delay;
- speed;
- bearing;
- occupancy;
- current stop; and
- predicted time

describe changing state and shall not by themselves establish semantic
identity.

---

### 3.4 Feed Entity Identity

`FeedEntity.id` is a feed-unique identifier used for realtime feed management
and incrementality.

It shall not automatically be used as the semantic identifier of:

- a PhysicalVehicle;
- a DatedVehicleJourney;
- a MonitoredVehicleJourney;
- a PublicTransportStop; or
- another domain entity.

It may be preserved as source provenance or feed-state metadata.

---

### 3.5 No Unjustified Semantics

A mapping shall not introduce semantic certainty that is absent from the
source data.

In particular:

- a past event time shall not automatically be classified as observed;
- absence of a TripUpdate shall not mean that a journey is on time;
- absence of uncertainty shall not mean zero uncertainty;
- absence of vehicle information shall not mean that no vehicle is assigned;
- absence of a realtime entity shall not necessarily mean that the
  corresponding operation does not exist.

---

### 3.6 Multi-Source Integration

TripUpdate and VehiclePosition information may describe different aspects of
the same operational journey.

Where source identifiers and source semantics establish that they concern the
same journey, their information may be integrated into one
MonitoredVehicleJourney.

Conflicting source information shall not be silently combined.

A mapping profile shall define:

- source precedence;
- timestamp comparison;
- conflict handling;
- freshness requirements; and
- provenance retention.

---

## 4. Core Correspondence

| GTFS-Realtime source | SCRA semantic concept | Mapping role |
|---|---|---|
| `TripDescriptor` | DatedVehicleJourney | Identifies the dated operational journey |
| `TripUpdate` | MonitoredVehicleJourney | Updates operational progress and predictions |
| `VehicleDescriptor` | PhysicalVehicle | Identifies or describes the assigned physical vehicle |
| `VehiclePosition` | MonitoredVehicleJourney and PhysicalVehicle | Updates journey execution and physical-vehicle state |
| `Position` | PhysicalVehicle location or monitored journey location | Provides observed geographic state |
| `StopTimeUpdate` | Passing-time information at a PointInJourneyPattern | Updates arrival, departure, delay, and stop status |
| `StopTimeEvent` | Estimated or observed temporal information | Provides absolute time, delay, and uncertainty |
| `schedule_relationship` | Dated or monitored journey status | Describes relationship to the static schedule |
| `timestamp` | Observation or report time | Provides temporal provenance |
| `Alert` | Operational alert or disruption concept | Deferred to a later specification |

The table defines semantic correspondence and does not require one source
message to become one semantic entity.

---

## 5. Feed-Level Mapping

### 5.1 FeedMessage

A `FeedMessage` is a source exchange container.

It shall not normally become a canonical public transport semantic entity.

Its metadata may be retained as provenance, including:

- feed version;
- incrementality mode;
- feed timestamp;
- source identifier;
- retrieval time; and
- source endpoint.

### 5.2 Full and Differential Feeds

For a full-dataset feed, absence from a later feed may affect the current
source representation according to GTFS-Realtime semantics.

For a differential feed, `is_deleted` may indicate removal of the feed
entity identified by `FeedEntity.id`.

Removal of a feed entity shall not automatically be interpreted as deletion
of the corresponding real-world PhysicalVehicle or planned journey.

The mapping shall distinguish:

- deletion from the source feed state;
- termination of monitoring;
- cancellation of a journey; and
- deletion of a semantic entity.

---

## 6. TripDescriptor Mapping

### 6.1 Semantic Role

A `TripDescriptor` provides source information used to identify the journey
to which realtime information applies.

It normally contributes to the identification or construction of a
DatedVehicleJourney.

### 6.2 Field Correspondence

| GTFS-Realtime field | Semantic interpretation |
|---|---|
| `trip_id` | Reference to the underlying ServiceJourney where applicable |
| `route_id` | Reference to PublicTransportRoute where supplied |
| `direction_id` | Direction classification |
| `start_date` | OperatingDay or service-date context |
| `start_time` | Additional dated-journey disambiguation |
| `schedule_relationship` | Relationship of the journey to the static schedule |

### 6.3 Dated Journey Identity

For a scheduled journey, DatedVehicleJourney identity normally requires:

- the applicable ServiceJourney;
- the OperatingDay; and
- any additional source information required to distinguish journey
  occurrences.

`trip_id` alone is generally insufficient because it identifies a recurring
GTFS Static trip rather than one dated occurrence.

### 6.4 Missing Static Trip

A realtime journey may not correspond directly to an existing static
ServiceJourney.

This may occur for added, replacement, duplicated, frequency-based, or other
source-defined operational journeys.

Such a journey may still be represented as a DatedVehicleJourney if its
dated operational identity can be established.

The mapping shall not invent an underlying ServiceJourney where none is
supported by the source data.

---

## 7. Schedule Relationship Mapping

The GTFS-Realtime schedule relationship shall be interpreted as operational
status and correspondence to the static schedule.

Typical interpretations include:

| GTFS-Realtime value | Semantic interpretation |
|---|---|
| `SCHEDULED` | Dated journey based on the applicable scheduled journey |
| `ADDED` or `NEW` | Additional dated journey not necessarily present in the recurring schedule |
| `UNSCHEDULED` | Journey without a fixed static schedule relationship |
| `CANCELED` | Cancellation state of the DatedVehicleJourney |
| `REPLACEMENT` | Dated journey replacing an applicable scheduled journey |
| `DUPLICATED` | New dated journey copied from an existing scheduled definition |
| `DELETED` | Source-defined removal from the applicable realtime representation |

The exact enum values available depend on the GTFS-Realtime specification
version and field context.

Cancellation changes the state of a DatedVehicleJourney but does not erase
its semantic identity.

Added, replacement, and duplicated journeys may require newly constructed
DatedVehicleJourney identities.

---

## 8. TripUpdate Mapping

### 8.1 Source Meaning

A `TripUpdate` provides realtime information about the progress of a vehicle
along a trip.

It may include:

- trip-level delay;
- stop-level predictions or reports;
- vehicle information;
- schedule relationship;
- trip properties; and
- report timestamp.

### 8.2 Semantic Target

A `TripUpdate` normally contributes operational state to one
MonitoredVehicleJourney.

It may also contribute information to:

- the corresponding DatedVehicleJourney;
- the assigned PhysicalVehicle;
- estimated passing-time information;
- observed passing-time information; and
- stop-level operational status.

### 8.3 Monitored Journey Construction

A TripUpdate shall not automatically create a separate
MonitoredVehicleJourney when an existing monitored instance with the same
semantic identity is already available.

Successive TripUpdates normally update the state of the same
MonitoredVehicleJourney.

### 8.4 Trip-Level Delay

A trip-level delay describes schedule deviation for the journey.

It shall be represented as operational state rather than semantic identity.

Where stop-level information conflicts with the trip-level delay, the
mapping shall preserve the more specific source semantics and document its
precedence policy.

### 8.5 Timestamp

`TripUpdate.timestamp` describes the time at which the realtime progress
information was measured or reported according to the source semantics.

It shall be preserved separately from:

- predicted arrival time;
- predicted departure time;
- observed arrival time;
- observed departure time; and
- feed retrieval time.

---

## 9. VehicleDescriptor Mapping

### 9.1 Semantic Target

A `VehicleDescriptor` may contribute to the identification and description of
one PhysicalVehicle.

### 9.2 Field Correspondence

| GTFS-Realtime field | Semantic interpretation |
|---|---|
| `id` | Source identifier for PhysicalVehicle |
| `label` | Passenger-facing or operational vehicle label |
| `license_plate` | Vehicle registration number |
| `wheelchair_accessible` | Source-defined operational accessibility indication |

### 9.3 Identity

`VehicleDescriptor.id` is the preferred GTFS-Realtime source identifier when
it is available and stable for the duration required by the mapping profile.

Its scope shall include the source authority or source feed.

`label` and `license_plate` shall not automatically be used as canonical
semantic identity.

### 9.4 Accessibility

`wheelchair_accessible` shall not automatically be interpreted as a permanent
intrinsic property of the PhysicalVehicle.

Its GTFS-Realtime meaning may concern the accessibility of the particular
trip or assignment.

The mapping shall preserve that operational scope.

---

## 10. VehiclePosition Mapping

### 10.1 Source Meaning

A `VehiclePosition` provides realtime information about a vehicle and,
normally, the journey it is performing.

### 10.2 Semantic Targets

A VehiclePosition may update both:

- one PhysicalVehicle; and
- one MonitoredVehicleJourney.

The two concepts shall remain distinct.

### 10.3 PhysicalVehicle State

The following fields may describe current PhysicalVehicle state:

| GTFS-Realtime field | Semantic interpretation |
|---|---|
| `vehicle` | PhysicalVehicle identity or description |
| `position.latitude` and `position.longitude` | Observed geographic location |
| `position.bearing` | Observed bearing |
| `position.speed` | Observed speed |
| `position.odometer` | Reported distance or odometer value |
| `occupancy_status` | Current occupancy classification |
| `occupancy_percentage` | Current reported occupancy percentage |
| `timestamp` | Time of observation or report |

### 10.4 MonitoredVehicleJourney State

The following fields may describe journey execution state:

| GTFS-Realtime field | Semantic interpretation |
|---|---|
| `trip` | DatedVehicleJourney context |
| `current_stop_sequence` | Current progress relative to JourneyPattern |
| `stop_id` | Current or related PublicTransportStop |
| `current_status` | Vehicle state relative to a stop |
| `congestion_level` | Reported operational congestion state |
| `timestamp` | Time of observation or report |

### 10.5 Location Semantics

A geographic position may be associated with the PhysicalVehicle because it
describes the physical asset's observed location.

The same observation may also contribute to the monitored execution state of
the journey.

The mapping shall avoid creating two contradictory location values from one
source observation.

### 10.6 Missing Trip Information

A VehiclePosition may identify a PhysicalVehicle without providing enough
information to identify a MonitoredVehicleJourney.

In that case, the PhysicalVehicle state may be mapped while the journey
relationship remains unresolved.

The mapping shall not invent a journey assignment.

---

## 11. StopTimeUpdate Mapping

### 11.1 Source Meaning

A `StopTimeUpdate` provides realtime updates for arrival and/or departure
events at a particular stop on a trip.

Updates may concern future events and may also include past events.

### 11.2 Pattern-Point Resolution

A StopTimeUpdate shall be resolved, where possible, to one
PointInJourneyPattern.

Resolution may use:

- `stop_sequence`;
- `stop_id`;
- the applicable ServiceJourney;
- the applicable JourneyPattern; and
- the GTFS Static stop-time sequence.

### 11.3 Repeated Stops

Where the same `stop_id` occurs multiple times in one journey,
`stop_sequence` or equivalent context is required to identify the correct
PointInJourneyPattern.

A mapping shall not identify an occurrence solely by `stop_id` when that
would be ambiguous.

### 11.4 Stop Sequence

`stop_sequence` is a source ordering value.

It contributes to PointInJourneyPattern resolution but shall not
automatically become the global semantic identifier of that point.

---

## 12. Passing-Time Mapping

### 12.1 Semantic Family

StopTimeUpdate information shall be interpreted using the applicable
passing-time semantics.

Relevant concepts include:

- TimetabledPassingTime;
- TargetPassingTime;
- EstimatedPassingTime; and
- ObservedPassingTime.

The detailed normative definitions of this family are completed separately.

### 12.2 Static and Realtime Distinction

Static scheduled values originate from GTFS Static and correspond to
timetabled information.

Realtime predictions normally correspond to EstimatedPassingTime.

A recorded event may correspond to ObservedPassingTime only when the source
semantics provide sufficient evidence that the event actually occurred.

### 12.3 Estimated versus Observed

`StopTimeEvent.time` may represent an estimated or an actual event.

A mapper shall not classify the value as ObservedPassingTime merely because:

- the value is earlier than the current clock time;
- the affected stop is earlier in the stop sequence; or
- a later update has been received.

Observed classification requires sufficient source or provider-specific
evidence.

Where that distinction cannot be established, the mapping shall preserve the
source value without introducing unsupported certainty.

### 12.4 Arrival and Departure

Arrival and departure are separate temporal aspects associated with the same
journey and pattern-point context.

A source update may provide:

- arrival only;
- departure only; or
- both arrival and departure.

The mapping shall not invent a missing event value unless an explicit and
documented source rule permits it.

### 12.5 Delay

`StopTimeEvent.delay` describes deviation from an applicable scheduled time.

It shall not replace the event time or passing-time concept.

Delay may be represented alongside the absolute estimated or observed time.

### 12.6 Uncertainty

`StopTimeEvent.uncertainty` shall be preserved where provided.

An omitted uncertainty value means that uncertainty is unknown.

It shall not be interpreted as zero uncertainty.

### 12.7 Stop-Time Schedule Relationship

Stop-level schedule relationship values may describe conditions such as:

- scheduled service;
- skipped stop;
- no realtime data;
- unscheduled stop; or
- another source-defined relationship.

A skipped stop changes the dated or monitored stopping plan.

It does not delete the PublicTransportStop or PointInJourneyPattern from the
underlying recurring JourneyPattern.

---

## 13. PhysicalVehicle Assignment

A MonitoredVehicleJourney may be related to the PhysicalVehicle performing
the journey.

The relationship is conceptually:

    MonitoredVehicleJourney
            |
            | operatedByVehicle
            v
      PhysicalVehicle

The assignment may be updated during operation.

Changing the assigned PhysicalVehicle does not change the semantic identity
of the DatedVehicleJourney.

A mapping profile shall define whether and how vehicle substitution affects
the identity of the MonitoredVehicleJourney.

---

## 14. Current Stop and Journey Progress

VehiclePosition and TripUpdate may provide information about progress through
the journey.

Progress shall be interpreted relative to the JourneyPattern and
PointInJourneyPattern structure where possible.

Typical semantic state includes:

- current PointInJourneyPattern;
- current PublicTransportStop;
- next PointInJourneyPattern;
- current stop sequence;
- status relative to the stop; and
- journey progress.

The source `stop_id` and `current_stop_sequence` shall not be copied without
resolving their semantic context where the required static information is
available.

---

## 15. Occupancy

Occupancy information describes changing operational state.

It may be associated with:

- the PhysicalVehicle;
- the MonitoredVehicleJourney;
- a carriage or vehicle section; or
- another source-defined operational unit.

The mapping shall preserve the scope supplied by the source.

An occupancy classification and an occupancy percentage are distinct
representations and shall not be silently substituted for one another.

---

## 16. Alerts and Disruptions

GTFS-Realtime Alerts describe disruptions, causes, effects, informed
entities, active periods, and passenger-facing information.

The normative semantic model for alerts and disruptions is deferred to a
later SCRA specification.

A conforming initial implementation may preserve Alert data as source
information without treating the GTFS Alert message structure as the
canonical semantic model.

Alert information shall not be embedded directly into unrelated
MonitoredVehicleJourney or PhysicalVehicle properties where a separate
disruption concept is required.

---

## 17. Provenance and Observation Time

A conforming mapping should preserve sufficient provenance to identify:

- source feed;
- source authority;
- FeedEntity identifier;
- source message type;
- feed timestamp;
- entity timestamp;
- retrieval time;
- mapping profile version; and
- static GTFS dataset context.

Observation time, prediction time, source publication time, and retrieval
time are distinct temporal concepts.

They shall not be collapsed into one timestamp without explicit semantics.

---

## 18. Update and Merge Behaviour

Successive GTFS-Realtime messages normally update existing semantic state.

A conforming implementation shall define:

- semantic identity resolution;
- entity matching;
- update ordering;
- stale-data handling;
- field removal;
- differential-feed deletion;
- source conflict handling; and
- monitoring-expiration rules.

A newer message shall not automatically overwrite a value from another
source unless the applicable precedence and freshness policy supports that
operation.

---

## 19. Conformance

A GTFS-Realtime mapping conforms to this specification when it:

- interprets realtime information in relation to the applicable GTFS Static
  context;
- distinguishes DatedVehicleJourney, MonitoredVehicleJourney, and
  PhysicalVehicle;
- does not use `FeedEntity.id` as domain identity without justification;
- resolves stop updates to PointInJourneyPattern where possible;
- distinguishes static, estimated, target, and observed timing semantics;
- does not infer observed events solely from temporal position;
- preserves uncertainty when supplied;
- distinguishes operational state from semantic identity;
- preserves source and temporal provenance;
- supports N-to-M correspondence between source messages and semantic
  instances; and
- does not introduce unsupported semantic certainty.

---

## 20. References

- GTFS-Realtime Reference:
  https://gtfs.org/documentation/realtime/reference/

- GTFS-Realtime Trip Updates:
  https://gtfs.org/documentation/realtime/feed-entities/trip-updates/

- GTFS-Realtime Vehicle Positions:
  https://gtfs.org/documentation/realtime/feed-entities/vehicle-positions/

- GTFS-Realtime Best Practices:
  https://gtfs.org/documentation/realtime/realtime-best-practices/

- GTFS Schedule Reference:
  https://gtfs.org/documentation/schedule/reference/

- Transmodel:
  https://www.transmodel-cen.eu/

- SCRA Semantic Modeling Principles:
  ../specifications/semantic-modeling-principles.md

- SCRA Semantic Models:
  ../models/index.md

- SCRA GTFS Static Mapping:
  gtfs-static.md

---

© SCRA Project
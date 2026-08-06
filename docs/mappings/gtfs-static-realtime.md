# GTFS Static and Realtime Mapping

## Status

Draft

## Purpose

This specification defines the normative mapping from GTFS Schedule and
GTFS Realtime source data to the SCRA mobility semantic models used for
journeys, ordered journey-pattern points, monitored operations, and passing
events.

The mapping preserves source semantics. A source field shall not be mapped to
a stronger SCRA semantic category unless the source provides sufficient
evidence for that category.

---

## Scope

This specification covers:

- GTFS Schedule `stops.txt`;
- GTFS Schedule `trips.txt`;
- GTFS Schedule `stop_times.txt`;
- GTFS Schedule `calendar.txt` and `calendar_dates.txt` where required for
  service-date resolution;
- GTFS Realtime `TripUpdate`;
- GTFS Realtime `TripUpdate.StopTimeUpdate`;
- GTFS Realtime `VehiclePosition`;
- GTFS Realtime feed and entity timestamps relevant to the mapped semantics.

The following SCRA concepts are in scope:

- `PublicTransportStop`;
- `JourneyPattern`;
- `PointInJourneyPattern`;
- `DatedVehicleJourney`;
- `MonitoredVehicleJourney`;
- `PassingEvent`;
- `scheduledTime`;
- `predictedTime`;
- `observedTime`;
- `observationTimestamp`;
- `delay`;
- `predictionConfidence`;
- `referenceTime`;
- `minutesUntilArrival`.

---

## General Mapping Principles

1. Source identifiers shall be interpreted within a source namespace. A GTFS
   identifier shall not be assumed to be globally unique outside its feed or
   profile-defined namespace.
2. GTFS Schedule defines planned structures and baseline times. GTFS Realtime
   updates the operational state of those planned structures or describes
   additional operational executions.
3. A `PublicTransportStop` identifies a referenced stop-related location. It
   does not by itself identify an occurrence within a journey.
4. A `PointInJourneyPattern` identifies an ordered occurrence within a
   `JourneyPattern`.
5. A `PassingEvent` identifies an arrival, departure, or passing event for one
   monitored journey at one ordered occurrence.
6. A past timestamp shall not be classified as `observedTime` solely because
   it is earlier than the processing time. Source semantics shall provide
   sufficient evidence that the event occurred.
7. Feed retrieval time, feed publication time, observation time, event time,
   and calculation reference time shall remain distinct.
8. Experimental GTFS fields may be mapped only by a profile that documents
   their stability expectations and fallback behavior.

---

## GTFS Schedule Mapping

### `stops.txt`

| GTFS field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| source/feed identity | `PublicTransportStop` identity namespace | Required | The feed or profile-defined source namespace shall participate in identity construction. |
| `stop_id` | `PublicTransportStop` identity component | Required | Map as the source-local stop-location identifier. It shall not be used alone as a globally unique identifier. |
| `location_type` | PublicTransportStop subtype or profile classification | Conditional | Preserve the GTFS location category. A profile shall distinguish stop/platform, station, entrance/exit, generic node, and boarding area where those values occur. |
| `parent_station` | parent-location relationship | Conditional | Resolve to the parent stop-location entity in the same source namespace. |
| `stop_name` | passenger-facing name | Conditional | Preserve as the primary passenger-facing name when supplied. |
| `stop_code` | passenger-facing code | Optional | Preserve separately from `stop_id`. |
| `stop_lat`, `stop_lon` | geographic location | Conditional | Map to the geographic position when coordinates are provided and valid. |
| `platform_code` | platform code | Optional | Preserve as passenger-facing platform information. |
| `stop_timezone` | local-time metadata | Optional | Preserve as stop metadata; it shall not override the agency timezone semantics used by GTFS stop times. |

A `stops.txt` record may represent multiple kinds of locations. A mapper shall
not assume that every `stop_id` denotes the same physical granularity.

### `trips.txt` and Service-Date Resolution

| GTFS field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| source/feed identity | journey identity namespace | Required | Include the source namespace in all mapped journey identities. |
| `trip_id` | `DatedVehicleJourney` or scheduled-journey identity component | Required | Resolve the scheduled trip within the source namespace. |
| `service_id` | operating-day resolution input | Required | Resolve applicable service dates using `calendar.txt` and/or `calendar_dates.txt`. |
| resolved service date | `DatedVehicleJourney` operating day | Required for dated execution | A dated execution shall be distinguished by the applicable service date. |
| `route_id` | route relationship | Required | Resolve to the corresponding route representation. |
| `direction_id` | direction classification | Optional | Preserve when supplied. |
| `shape_id` | shape relationship | Optional | Resolve when the profile represents shapes. |
| `block_id` | block relationship | Optional | Preserve for operational grouping; it is not journey identity by itself. |

A GTFS `trip_id` alone shall not identify a real-time monitored execution when
more than one execution can correspond to the same scheduled trip. The
applicable service date and, when required, a start time or another execution
discriminator shall also participate.

### `stop_times.txt`

The GTFS primary key `(trip_id, stop_sequence)` maps naturally to the ordered
occurrence represented by `PointInJourneyPattern`.

| GTFS field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| `trip_id` | parent `JourneyPattern` / scheduled journey resolution | Required | Resolve the pattern or scheduled journey to which the point belongs. |
| `stop_sequence` | `PointInJourneyPattern` occurrence discriminator | Required | Use as the primary ordering and occurrence discriminator unless a profile defines a more stable equivalent. |
| `stop_id` | `PublicTransportStop` relationship | Required for ordinary stop records | Resolve the referenced stop entity. It shall not by itself identify the ordered occurrence. |
| `arrival_time` | arrival `PassingEvent.scheduledTime` | Conditional | Create or enrich the scheduled arrival event when supplied. |
| `departure_time` | departure `PassingEvent.scheduledTime` | Conditional | Create or enrich the scheduled departure event when supplied. |
| `pickup_type` | boarding permission | Optional | Map to the corresponding PointInJourneyPattern boarding semantics. |
| `drop_off_type` | alighting permission | Optional | Map to the corresponding PointInJourneyPattern alighting semantics. |
| `timepoint` | timing-point indication | Optional | Preserve whether the time is exact or approximate according to the applicable profile. |
| `stop_headsign` | stop-specific destination/headsign | Optional | Preserve separately from journey-wide destination information. |
| `shape_dist_traveled` | distance-along-pattern | Optional | Preserve when supported by the profile. |

Each `stop_times.txt` record shall normally produce one
`PointInJourneyPattern`. Repeated use of the same `stop_id` in one trip shall
produce distinct ordered occurrences when the `stop_sequence` values differ.

Where both arrival and departure are represented, they shall be distinct
`PassingEvent` entities or distinct operational event identities, even when
their scheduled time values are equal.

---

## GTFS Realtime Trip Mapping

### `TripDescriptor`

| GTFS Realtime field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| `trip_id` | scheduled journey identity component | Conditional | Resolve to the GTFS Schedule trip when present. |
| `start_date` | operating day / service-date discriminator | Conditional | Use in monitored-execution identity when supplied or required by GTFS semantics. |
| `start_time` | execution discriminator | Conditional | Use when multiple executions may share the same scheduled identity, including frequency-based or unscheduled service. |
| `schedule_relationship` | operational relationship/state | Conditional | Preserve the relationship to the static schedule; do not infer ordinary scheduled operation for values that represent added, replacement, canceled, duplicated, or unscheduled service. |
| `route_id` | route resolution fallback | Conditional | Use according to GTFS Realtime requirements when `trip_id` is absent or the trip is newly defined. |
| `direction_id` | direction classification | Optional | Preserve when supplied. |

The mapped `MonitoredVehicleJourney` identity shall remain stable for the
duration of one monitored execution. Vehicle assignment is not part of that
identity.

### `TripUpdate`

| GTFS Realtime field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| `trip` | `MonitoredVehicleJourney` | Required | Resolve or create the monitored execution using the TripDescriptor identity rules. |
| `vehicle` | `PhysicalVehicle` relationship | Optional | Relate the monitored journey to the currently assigned vehicle when known. |
| `timestamp` | `observationTimestamp` candidate | Optional | Map only when it represents the observation or report time supporting the TripUpdate semantics. It shall not be used as an event time. |
| `delay` | journey-level operational delay | Optional | Preserve as journey-level delay only. It shall not replace stop-specific delay when a StopTimeUpdate supplies one. |
| `stop_time_update` | `PassingEvent` updates | Optional/repeated | Map each applicable arrival and departure update according to the rules below. |

Absence of a TripUpdate shall mean that no real-time update is available; it
shall not be interpreted as on-time operation.

---

## `TripUpdate.StopTimeUpdate` Mapping

### Occurrence Resolution

A StopTimeUpdate shall be resolved to one ordered occurrence by the following
rules:

1. When `stop_sequence` is supplied, resolve the matching
   `PointInJourneyPattern` occurrence.
2. When only `stop_id` is supplied, resolve it only if the applicable journey
   contains exactly one unambiguous occurrence of that stop.
3. When the same stop occurs more than once and `stop_sequence` is absent, the
   update is ambiguous and shall not be silently assigned to one occurrence.
4. Experimental reassignment fields such as `assigned_stop_id` shall be handled
   only by an applicable profile and shall preserve the original occurrence
   identity.

### Event Mapping

For each resolved occurrence:

| GTFS Realtime field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| `arrival` | arrival `PassingEvent` | Conditional | Map to `eventType = arrival`. |
| `departure` | departure `PassingEvent` | Conditional | Map to `eventType = departure`. |
| `stop_sequence` | occurrence discriminator | Conditional | Preserve for PointInJourneyPattern and PassingEvent identity resolution. |
| `stop_id` | `stop` relationship resolution | Conditional | Resolve the referenced PublicTransportStop; do not use alone for repeated-stop identity. |
| `schedule_relationship` | event operational state | Optional | Preserve `SCHEDULED`, `SKIPPED`, `NO_DATA`, `UNSCHEDULED`, or other supported values according to an applicable profile. |
| `stop_time_properties.assigned_stop_id` | real-time stop assignment | Experimental/optional | A profile may map the newly assigned stop while preserving occurrence identity and documenting fallback behavior. |

A StopTimeUpdate with both arrival and departure shall map to two
`PassingEvent` operational identities unless the applicable profile explicitly
models a single undifferentiated passing event.

### `StopTimeEvent`

| GTFS Realtime field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| `time` | normally `predictedTime` | Optional | Map as `predictedTime` when it is a real-time forecast. Map as `observedTime` only when source semantics explicitly establish that the event occurred. |
| `delay` | `delay` | Optional | Map in whole seconds. Positive means later than baseline, negative means earlier, and zero means no difference. |
| `uncertainty` | confidence/uncertainty input | Optional | Do not map mechanically to `predictionConfidence`. A profile shall define the qualitative conversion, units, and thresholds. |
| scheduled baseline from static GTFS | `scheduledTime` | Conditional | Resolve the applicable arrival or departure baseline from `stop_times.txt`. |

When both absolute `time` and `delay` are supplied, the mapper shall retain the
absolute predicted or observed time and may validate the delay against the
scheduled baseline. A conflict policy shall be defined by the applicable
mapping profile; the values shall not be silently reconciled.

`observedTime` shall not be inferred merely because a StopTimeUpdate describes
a past stop. GTFS Realtime permits StopTimeUpdates for past and future stop
times.

### Derived Passenger-Facing Values

| SCRA property | Source inputs | Mapping rule |
|---|---|---|
| `referenceTime` | mapper calculation time | Use an offset-aware ISO 8601 timestamp representing the calculation basis. |
| `minutesUntilArrival` | selected arrival time and `referenceTime` | Apply the normative SCRA calculation: `predictedTime` when available, otherwise `scheduledTime`; truncate positive durations toward zero and clamp past values to `0`. |
| `predictionConfidence` | source quality evidence | Use only when a documented profile maps available GTFS Realtime evidence to `high`, `medium`, `low`, or `none`. Property absence means confidence is not stated or unknown. |
| `observationTimestamp` | TripUpdate or source observation/report timestamp | Preserve only when its semantics represent the supporting observation or report time. |

---

## GTFS Realtime Vehicle Mapping

### `VehiclePosition`

| GTFS Realtime field | SCRA target | Requirement | Mapping rule |
|---|---|---:|---|
| `trip` | `MonitoredVehicleJourney` | Conditional | Resolve the monitored execution using the same TripDescriptor identity rules as TripUpdate. |
| `vehicle` | `PhysicalVehicle` relationship or identity | Optional | Preserve the physical vehicle identifier when supplied. |
| `position.latitude`, `position.longitude` | current geographical position | Optional | Map as the vehicle or monitored-journey position according to the applicable profile. |
| `position.bearing` | current bearing | Optional | Preserve in degrees according to GTFS semantics. |
| `position.speed` | current speed | Optional | Preserve with an explicit unit compatible with the source specification. |
| `timestamp` | position observation time | Optional | Map as the observation time of the position reading, not as feed publication time or passing-event time. |
| `current_stop_sequence` | current PointInJourneyPattern occurrence | Optional | Resolve to the corresponding ordered occurrence when possible. |
| `stop_id` | current PublicTransportStop reference | Optional | Use for stop resolution; repeated-stop occurrence may still require `current_stop_sequence`. |
| `current_status` | vehicle progress state | Optional | Preserve according to the applicable profile. |
| `occupancy_status`, `occupancy_percentage` | occupancy information | Optional | Preserve with its observation context and source semantics. |
| `congestion_level` | congestion classification | Optional | Preserve according to a documented profile. |

The VehiclePosition timestamp is distinct from the FeedHeader timestamp. The
former may represent when the position reading was taken; the latter
represents feed-message generation or publication context.

---

## Feed and Entity Metadata

| GTFS Realtime field | SCRA interpretation | Mapping rule |
|---|---|---|
| `FeedHeader.timestamp` | feed publication/generation time | Keep separate from `observationTimestamp`, `referenceTime`, and event times unless a profile establishes equivalent semantics. |
| `FeedEntity.id` | source message-entity identifier | Preserve for provenance or source correlation. It shall not automatically become the semantic identity of a MonitoredVehicleJourney or PassingEvent. |
| retrieval time | retrieval metadata | Record separately from all source timestamps. |
| mapper processing time | processing metadata | Record separately; use as `referenceTime` only when it is the actual calculation basis for a derived value. |

---

## PassingEvent Identity

A mapped `PassingEvent` identity shall distinguish at least:

- the applicable `MonitoredVehicleJourney`;
- the operational event type;
- the ordered occurrence represented by a `PointInJourneyPattern`, its
  sequence, or an equivalent profile-defined discriminator.

A `PublicTransportStop` identifier alone is insufficient where the same
journey visits the same stop more than once.

Changes to scheduled, predicted, observed, delay, confidence, observation, or
derived passenger-facing values shall update the same `PassingEvent` and shall
not create a new semantic identity.

---

## Conformance

A conforming GTFS-to-SCRA mapper shall:

- preserve source namespaces in mapped identities;
- distinguish planned structure from monitored operational execution;
- preserve repeated-stop occurrences;
- keep arrival and departure operational identities distinct;
- distinguish scheduled, predicted, and observed event times;
- avoid deriving `observedTime` from temporal position alone;
- preserve the sign and whole-second unit of GTFS delay values;
- keep feed, retrieval, observation, processing, reference, and event times
  semantically distinct;
- document any mapping from GTFS uncertainty or other quality indicators to
  `predictionConfidence`;
- document handling of experimental GTFS fields;
- expose validation failures or ambiguities rather than silently assigning
  source data to an uncertain semantic entity.

---

## Mobility MCP Implementation Checkpoints

The Mobility MCP reference implementation shall be checked against this
mapping for at least the following behavior:

| Checkpoint | Expected behavior |
|---|---|
| Monitored journey identity | Includes source-scoped scheduled identity, service date, and any required execution discriminator. |
| Point occurrence identity | Uses `stop_sequence` or an equivalent discriminator and does not rely on `stop_id` alone for repeated stops. |
| PassingEvent identity | Includes monitored journey, occurrence discriminator, and event type. |
| Arrival/departure separation | Produces distinct event identities where both are represented. |
| Time classification | Maps forecasts to `predictedTime`; maps actual occurrences to `observedTime` only with source evidence. |
| Delay | Preserves signed whole seconds and the applicable scheduled baseline. |
| Observation timestamp | Does not substitute retrieval or feed-header time without documented semantics. |
| Reference time | Is offset-aware and is the actual basis for derived values. |
| Minutes until arrival | Implements the normative calculation and clamping policy. |
| Prediction confidence | Uses only the normative value space and a documented conversion policy. |
| NGSI-LD context | Expands all compact terms to the expected SCRA vocabulary IRIs. |
| Ambiguous stop update | Rejects or reports repeated-stop ambiguity when no occurrence discriminator is available. |

---

## Source Specifications

This mapping is based on the official GTFS Schedule and GTFS Realtime
specifications. Informative source references shall not override the normative
SCRA rules defined in this document.

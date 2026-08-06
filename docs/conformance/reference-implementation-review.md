# Reference Implementation Conformance Review

## Status

Draft baseline for SCRA Version 1.0 Release Candidate review and approval.

## Baseline Statement

This document defines the implementation conformance baseline for the SCRA
Version 1.0 Release Candidate.

After approval of this baseline, changes to the Mobility MCP Reference
Implementation shall be made to conform to this review and to the applicable
normative SCRA specifications.

Conformance shall be demonstrated through the SCRA Conformance Test Suite
before the Version 1.0 Release Candidate is published.

A change to an applicable normative requirement or to an approved baseline
finding shall require review and approval of an updated baseline before the
implementation is changed to rely on that change.

## Scope

This review evaluates the current Mobility MCP Reference Implementation
against the normative SCRA vocabulary, semantic models, GTFS Static and GTFS
Realtime mapping rules, and worked examples prepared for Version 1.0.

The review covers:

- `PassingEventMapper`;
- `JourneyResolver`;
- `GTFSTripUpdateMapper`;
- `GTFSVehiclePositionMapper`;
- the existing unit tests directly associated with those components.

This review records implementation conformance and identified gaps. It does
not modify the Reference Implementation and does not itself constitute a test
result.

## Conformance Status Values

The following status values are used:

- **Conformant**: the reviewed implementation behavior satisfies the applicable
  requirement.
- **Partially Conformant**: some required behavior is present, but one or more
  applicable constraints are missing or insufficiently enforced.
- **Non-Conformant**: the implemented behavior conflicts with an applicable
  normative requirement.
- **Not Implemented**: the applicable required or planned behavior is absent.
- **Conformance Risk**: the implementation may produce a non-conformant result
  under identifiable conditions, but the reviewed evidence does not establish
  that the condition always occurs.

## Reviewed Components

### PassingEvent Mapper

#### Requirement

A conforming `PassingEvent` shall have a stable identity based on the monitored
journey execution, the applicable ordered stop occurrence, and `eventType`.
Arrival and departure events at the same occurrence shall have distinct
identities.

A conforming representation shall distinguish `scheduledTime`,
`predictedTime`, and `observedTime`. `predictionConfidence` applies only to
predicted operational values. `delay` shall preserve signed whole seconds and
shall be supported by the applicable operational and baseline times.
`minutesUntilArrival` shall use the normative calculation and shall be
accompanied by `referenceTime`.

#### Implementation Evidence

The current mapper:

- creates a `PassingEvent`;
- creates `monitoredVehicleJourney` and `stop` Relationships;
- emits `eventType` with the fixed value `arrival`;
- emits `scheduledTime`, `predictedTime`, `minutesUntilArrival`,
  `predictionConfidence`, `referenceTime`, and `delay` when supplied by the
  estimate model;
- constructs identity from trip identity, physical stop identity, service
  date, and the fixed arrival event designation.

The mapper does not receive or retain a `PointInJourneyPattern`,
`stop_sequence`, or another ordered-occurrence discriminator.

The mapper emits `predictedTime` from the generic estimated time without
independently verifying that the estimation method represents a prediction.
It does not emit `observedTime`. It emits `predictionConfidence` even when no
predicted operational value is available.

The mapper passes through `minutesUntilArrival`; the current estimator
calculation rounds positive partial minutes upward rather than truncating them
toward zero.

#### Existing Tests

Existing tests verify:

- basic `PassingEvent` creation;
- emitted Relationships and Properties;
- fixed `eventType = arrival`;
- stable identity for identical input;
- delay value and `SEC` unit;
- basic argument type validation.

The tests do not verify:

- repeated-stop occurrence identity;
- arrival and departure identity separation;
- service-date and execution discrimination;
- schedule-only suppression of `predictedTime`;
- explicit `observedTime`;
- the conditional use of `predictionConfidence`;
- offset-aware date-time rejection at the mapper boundary;
- normative `minutesUntilArrival` cases such as 59 seconds, 119 seconds, and
  past arrival times.

#### Conformance Status

| Review item | Status |
|---|---|
| Basic `PassingEvent` construction | Conformant |
| Occurrence-based identity | Non-Conformant |
| Arrival/departure identity separation | Not Implemented |
| `eventType` value handling | Partially Conformant |
| `scheduledTime` mapping | Partially Conformant |
| `predictedTime` semantic classification | Non-Conformant |
| `observedTime` support | Not Implemented |
| `delay` representation | Partially Conformant |
| `predictionConfidence` conditional use | Non-Conformant |
| `minutesUntilArrival` Property output | Conformant |
| Normative `minutesUntilArrival` calculation | Non-Conformant |
| `referenceTime` conditional relationship | Partially Conformant |

#### Gap

The mapper lacks ordered-occurrence identity, supports only arrival events,
does not distinguish all temporal semantics, and relies on an estimator whose
minute calculation does not conform to the normative truncation rule.

`minutesUntilArrival` alone is also insufficient for close-range
accessibility guidance. General search results may present whole minutes, but
accessibility presentation shall support seconds, equivalent precision, or
staged spoken expressions such as "arriving soon" as defined by the vocabulary
guidance.

#### Priority

**Priority 1** for identity, temporal semantics, confidence conditions, and the
normative minute calculation.

---

### Journey Resolver

#### Requirement

Journey resolution shall preserve the ordered occurrence at which a journey
serves a stop. Repeated visits to the same `PublicTransportStop` within one
journey pattern shall remain distinct.

For GTFS, `stop_sequence` shall be used as the primary occurrence
discriminator unless a profile defines a more stable equivalent. A physical
`stop_id` shall not by itself identify the occurrence.

A resolver shall not silently select one occurrence when a repeated-stop
reference is ambiguous.

#### Implementation Evidence

The current resolver:

- resolves and ranks trip-like entities serving a boarding stop;
- may resolve the associated `JourneyPattern`;
- produces deterministic candidate ordering;
- validates candidate-result limits;
- uses a semantic-cache index to determine whether a trip serves a stop.

`JourneyResolutionCandidate` contains the trip, physical boarding stop,
destination candidate, route, and JourneyPattern. It does not contain a
`PointInJourneyPattern`, `stop_sequence`, or another occurrence discriminator.

Candidate deduplication uses physical stop identity plus trip identity.
Consequently, repeated occurrences of the same stop in the same trip are
collapsed into one candidate.

The resolver does not expose an ambiguity state for repeated stops. It also
permits suffix-based Relationship matching, which may match equal native
identifiers across different source namespaces.

#### Existing Tests

No dedicated Journey Resolver unit-test file was found by the repository
search used for this review.

#### Conformance Status

| Review item | Status |
|---|---|
| Deterministic ranking | Conformant |
| Limit validation | Conformant |
| Physical trip-stop membership detection | Partially Conformant |
| `JourneyPattern` resolution | Partially Conformant |
| `PointInJourneyPattern` resolution and retention | Not Implemented |
| `stop_sequence` handling | Not Implemented |
| Repeated-stop preservation | Non-Conformant |
| Repeated-stop ambiguity reporting | Not Implemented |
| Boarding-permission evaluation | Not Implemented |
| Source-scoped Relationship identity | Conformance Risk |
| Dedicated unit tests | Not Implemented |

#### Gap

The resolution unit is currently physical stop by trip rather than ordered
occurrence by monitored or planned journey. This prevents the resolved
candidate from supplying the occurrence identity required by downstream
`PassingEvent` construction.

#### Priority

**Priority 1** for occurrence preservation, repeated-stop ambiguity, and
source-scoped identity matching.

---

### TripUpdate Mapper

#### Requirement

A GTFS Realtime `TripUpdate` shall resolve a monitored journey execution.
Execution identity shall include the source, applicable trip, service date,
and an additional start-time or profile-defined discriminator when required.

Each applicable StopTimeUpdate arrival or departure shall map to a distinct
`PassingEvent`. `stop_sequence` shall resolve the ordered
`PointInJourneyPattern` occurrence. Forecast event times shall map to
`predictedTime`; `observedTime` requires explicit operational evidence.

#### Implementation Evidence

The current mapper:

- validates and maps GTFS Realtime `TripUpdate` messages;
- creates source-scoped static `Trip` entity identities;
- preserves TripDescriptor `start_date`, `start_time`, route, direction, and
  schedule relationship;
- preserves TripUpdate timestamp as internal `observed_at`;
- preserves StopTimeUpdate order and structured arrival/departure values;
- preserves `stop_sequence`, `stop_id`, event time, delay, and uncertainty;
- rejects a StopTimeUpdate having neither `stop_sequence` nor `stop_id`;
- rejects duplicate mapped Trip entity identifiers in one feed.

The mapper writes real-time information onto a `Trip` entity. It does not
create a `MonitoredVehicleJourney` or `PassingEvent` entities. StopTimeUpdates
are embedded as a structured Property.

Mapped identity is based on `trip_id` and source only. Service date and start
time do not participate in monitored-execution identity. Different executions
sharing one trip identifier may therefore be rejected as duplicates.

#### Existing Tests

Existing tests verify:

- source-scoped Trip identity;
- required `trip_id`;
- FeedMessage filtering and source order;
- duplicate Trip identifier rejection;
- deterministic repeated mapping.

The tests do not verify the detailed StopTimeUpdate mapping, execution
identity, `MonitoredVehicleJourney`, `PassingEvent`, repeated stops, temporal
classification, delay unit, uncertainty mapping, schedule relationships, or
TripUpdate observation timestamp.

#### Conformance Status

| Review item | Status |
|---|---|
| GTFS Realtime message validation | Conformant |
| Source-scoped static Trip identity | Conformant |
| `start_date` and `start_time` preservation | Conformant |
| Monitored execution identity | Non-Conformant |
| `MonitoredVehicleJourney` generation | Not Implemented |
| StopTimeUpdate source-data preservation | Conformant |
| Arrival/departure source distinction | Conformant |
| Arrival/departure `PassingEvent` separation | Not Implemented |
| `stop_sequence` preservation | Conformant |
| Ordered-occurrence resolution | Not Implemented |
| Repeated-stop ambiguity detection | Not Implemented |
| `predictedTime` classification | Not Implemented |
| `observedTime` classification | Not Implemented |
| Signed delay preservation | Conformant |
| Normative SCRA delay representation | Partially Conformant |
| Uncertainty preservation | Conformant |
| `predictionConfidence` mapping | Not Implemented |
| Internal observation-time propagation | Conformant |
| Explicit `observationTimestamp` mapping | Not Implemented |
| Schedule-relationship operational handling | Not Implemented |
| Unit-test coverage | Partially Conformant |

#### Gap

The mapper is a reliable source-preservation layer, but it does not implement
the normative SCRA entity separation among static journeys, monitored
executions, ordered occurrences, and passing events.

#### Priority

**Priority 1** for monitored-execution identity and generation of
`MonitoredVehicleJourney` and `PassingEvent` entities.

---

### VehiclePosition Mapper

#### Requirement

A GTFS Realtime `VehiclePosition` shall produce source-scoped vehicle state
with geographic and operational observation time.

TripDescriptor information shall support resolution of the applicable
`MonitoredVehicleJourney`. `current_stop_sequence` and `stop_id` shall retain
sufficient information to resolve the ordered occurrence without treating the
physical stop alone as occurrence identity.

The GTFS vehicle timestamp represents observation time. It shall remain
distinct from event time, retrieval time, and query-relative `referenceTime`.

#### Implementation Evidence

The current mapper:

- creates a source-scoped `Vehicle` entity;
- prefers a durable VehicleDescriptor ID and falls back to FeedEntity ID;
- maps location, bearing, speed, odometer, current stop, current stop sequence,
  current status, occupancy, route, and TripDescriptor information;
- converts VehiclePosition timestamp to an offset-aware UTC `observed_at`;
- applies that observation time to mapped attributes;
- rejects duplicate vehicle entities within one mapped feed.

The mapper relates the Vehicle directly to a static Trip. It preserves
`start_date` and `start_time`, but it does not resolve or relate the observation
to a `MonitoredVehicleJourney`.

It preserves `current_stop_sequence` and current physical stop but does not
resolve a `PointInJourneyPattern` occurrence.

`referenceTime` is not a responsibility of this source mapper and its absence
is not itself a conformance failure. It belongs to query-relative derivation
and presentation.

#### Existing Tests

Existing tests verify:

- source-scoped vehicle identity and ID fallback;
- location, bearing, speed, and odometer;
- internal observation timestamp propagation;
- Trip, Route, and current-stop Relationships;
- current stop sequence and operational Properties;
- TripDescriptor start date and start time;
- feed filtering, order, duplicate rejection, and error handling.

The tests do not verify:

- resolution of `MonitoredVehicleJourney`;
- PointInJourneyPattern occurrence resolution;
- repeated stops;
- explicit SCRA observation-time serialization;
- JSON-LD term expansion.

#### Conformance Status

| Review item | Status |
|---|---|
| Source-scoped Vehicle identity | Conformant |
| Geographic position mapping | Conformant |
| Bearing and speed mapping | Conformant |
| Current physical stop mapping | Conformant |
| `current_stop_sequence` preservation | Conformant |
| Offset-aware observation time | Conformant |
| Internal observation-time propagation | Conformant |
| Explicit SCRA observation-time serialization | Partially Conformant |
| TripDescriptor preservation | Conformant |
| `MonitoredVehicleJourney` resolution | Not Implemented |
| PointInJourneyPattern occurrence resolution | Not Implemented |
| Repeated-stop handling | Not Implemented |
| `referenceTime` in the source mapper | Not Applicable |
| Unit-test coverage | Partially Conformant |

#### Gap

Vehicle state is mapped well, but the implementation does not connect the
observation to the monitored execution and ordered journey occurrence required
by the normative semantic model.

#### Priority

**Priority 2**, except for changes required to support the Priority 1
monitored-execution and occurrence model.

## Cross-Component Findings

### Identity Chain

The implementation does not yet provide a complete identity chain:

```text
DatedVehicleJourney
    â†’ MonitoredVehicleJourney
        â†’ PointInJourneyPattern occurrence
            â†’ PassingEvent eventType
```

The absence of this chain causes repeated-stop ambiguity and prevents stable
arrival/departure event identities.

### Temporal Semantics

The implementation preserves many source timestamps but does not consistently
classify them into:

- `scheduledTime`;
- `predictedTime`;
- `observedTime`;
- `observationTimestamp`;
- `referenceTime`.

The classification shall be explicit and shall not rely solely on whether a
timestamp lies in the past.

### Accessibility

`minutesUntilArrival` remains useful for general search and conversational
results. It shall not be treated as sufficient for close-range accessibility
guidance. Presentation intended to support blind or visually impaired users
shall provide seconds, equivalent precision, or staged spoken expressions as
the vehicle approaches.

### JSON-LD

The normative vocabulary terms are registered in `mobility.jsonld`. The
reviewed Python mappers primarily produce internal Context models. End-to-end
tests are required to prove that serialization expands and compacts the
expected SCRA IRIs without introducing unregistered terms.

## Summary

### Conformant

The implementation already provides strong low-level behavior in:

- source-scoped GTFS entity construction;
- GTFS Realtime message validation;
- preservation of stop sequence, stop ID, timestamps, delays, and uncertainty;
- geographic vehicle-state mapping;
- deterministic ranking and feed-order handling;
- basic internal observation-time propagation.

### Partially Conformant

Partial conformance exists in:

- `PassingEvent` temporal Property mapping;
- JourneyPattern and physical stop resolution;
- normative delay representation;
- explicit observation-time serialization;
- component-level test coverage.

### Non-Conformant

The principal conflicts are:

- `PassingEvent` identity lacks an ordered-occurrence discriminator;
- repeated stop occurrences are collapsed by Journey Resolver;
- TripUpdate is mapped to a static `Trip` rather than a monitored execution;
- monitored-execution identity omits service-date and execution
  discrimination;
- schedule-only time may be emitted as `predictedTime`;
- `predictionConfidence` may be emitted without a predicted value;
- the current `minutesUntilArrival` calculation rounds upward.

### Not Implemented

The principal missing capabilities are:

- `PointInJourneyPattern` occurrence resolution and retention;
- repeated-stop ambiguity reporting;
- arrival/departure `PassingEvent` generation;
- explicit `observedTime` support;
- `MonitoredVehicleJourney` generation and resolution;
- explicit SCRA observation-time serialization where required;
- dedicated Journey Resolver tests;
- normative cross-component and JSON-LD conformance tests.

## Prioritized Remediation

### Priority 1 â€” Semantic Correctness

1. Define and implement monitored-execution identity using source, trip,
   service date, and any required execution discriminator.
2. Resolve and retain `PointInJourneyPattern` or an equivalent ordered
   occurrence using `stop_sequence`.
3. Preserve repeated-stop occurrences and report ambiguity when occurrence
   identity cannot be resolved.
4. Generate distinct arrival and departure `PassingEvent` entities.
5. Make `PassingEvent` identity depend on monitored execution, occurrence, and
   `eventType`.
6. Classify schedule, prediction, observation, and report timestamps
   explicitly.
7. Emit `predictionConfidence` only for predicted operational values.
8. Correct `minutesUntilArrival` to use truncation toward zero and clamp past
   values to zero.
9. Remove unsafe cross-source suffix matching from the normative resolution
   path.

### Priority 2 â€” Missing Normative Behavior

1. Apply boarding-permission and skipped/no-data semantics where relevant.
2. Produce normative `delay` Properties with `unitCode = SEC`.
3. Resolve VehiclePosition observations to the applicable
   `MonitoredVehicleJourney`.
4. Resolve current stop sequence to the applicable ordered occurrence.
5. Add explicit observation-time serialization in the SCRA presentation layer.
6. Define profile-controlled handling of GTFS uncertainty and
   `predictionConfidence`.
7. Preserve the accessibility distinction between general whole-minute
   results and close-range guidance.

### Priority 3 â€” Test and Publication Support

1. Add dedicated Journey Resolver tests.
2. Add PassingEvent identity and repeated-stop tests.
3. Add monitored-execution identity tests.
4. Add temporal-classification and confidence tests.
5. Add normative `minutesUntilArrival` boundary tests.
6. Add GTFS Static/Realtime integration tests.
7. Add JSON-LD expansion and compact-term tests.
8. Link each conformance test to the applicable baseline finding and normative
   requirement.

## Approval and Baseline Control

This document becomes an approved Version 1.0 RC baseline only after project
review and explicit approval.

After approval:

- Priority 1 implementation changes shall be traced to findings in this
  document;
- each resolved finding shall be demonstrated by the Conformance Test Suite;
- changes to baseline findings or applicable normative requirements shall be
  reviewed and approved before implementation relies on them;
- remaining accepted limitations shall be recorded in the Version 1.0 RC
  release notes.

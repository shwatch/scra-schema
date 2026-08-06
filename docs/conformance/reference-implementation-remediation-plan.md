# Reference Implementation Remediation Plan

## Status

Draft baseline execution plan for the SCRA Version 1.0 Release Candidate.

## Objective

This document defines the execution plan for bringing the Mobility MCP
Reference Implementation into conformance with the approved SCRA Version 1.0
RC implementation baseline.

It establishes:

- the order of implementation work;
- the scope of each remediation phase;
- the relationship between baseline findings, code changes, and tests;
- the immediate milestone for Qwen3 interoperability;
- the acceptance and release criteria for the Version 1.0 Release Candidate.

## Relationship to the Baseline Review

This plan shall be read together with:

- [`reference-implementation-review.md`](reference-implementation-review.md).

The approved Reference Implementation Conformance Review records the current
implementation status and identified gaps.

This plan defines how those findings shall be resolved.

Implementation work shall not redefine the applicable normative SCRA
semantics. Where a normative or baseline change is required, the applicable
baseline document shall be reviewed and approved before implementation relies
on that change.

## Scope

This plan covers:

- the Mobility MCP Reference Implementation;
- `JourneyResolver`;
- `PassingEventMapper`;
- `GTFSTripUpdateMapper`;
- `GTFSVehiclePositionMapper`;
- supporting context, identity, mapping, and presentation services;
- the SCRA Conformance Test Suite;
- regression testing;
- JSON-LD context validation;
- Version 1.0 RC publication documentation.

This plan does not reopen the normative vocabulary, semantic models, or GTFS
mapping rules except through an explicitly approved baseline change.

## Guiding Principles

- Normative specifications are frozen for the Version 1.0 RC implementation
  cycle unless an approved baseline change is made.
- The Reference Implementation shall conform to the approved Baseline Review.
- Priority 1 findings shall be resolved before the Version 1.0 RC is
  published.
- Each implementation change shall be traceable to an approved conformance
  finding.
- Conformance shall be demonstrated through the SCRA Conformance Test Suite.
- Existing behavior shall be preserved unless it conflicts with the approved
  baseline.
- Source-scoped semantic identity shall be preserved.
- Repeated-stop occurrences shall not be silently collapsed.
- Scheduled, predicted, observed, report, retrieval, and reference times shall
  remain semantically distinct.
- Documentation shall be synchronized before publication.
- Work shall proceed in small, testable, reversible steps.

## Current Project Status

### Completed

- Normative vocabulary review completed.
- `PassingEvent` semantic model review completed.
- `MonitoredVehicleJourney` semantic model review completed.
- `PointInJourneyPattern` semantic model review completed.
- GTFS Static and GTFS Realtime mapping specification completed.
- GTFS Realtime mapping examples completed.
- `minutesUntilArrival` accessibility guidance completed.
- Reference Implementation Conformance Review completed.
- Version 1.0 RC conformance baseline established.

### In Progress

- Reference Implementation Remediation Plan.
- Priority 1 implementation preparation.

### Immediate Next Milestone

Complete the Priority 1 implementation work required to demonstrate
end-to-end interoperability between the Mobility MCP Reference Implementation
and Qwen3.

The objective of this milestone is to demonstrate that an AI client can
retrieve mobility information through the Mobility MCP Reference
Implementation using the normative SCRA semantic model.

The Qwen3 connection is the first implementation proof of interoperability for
the Version 1.0 RC work. It is not the final release gate.

Successful completion of this milestone establishes the implementation
baseline required for the SCRA Version 1.0 Release Candidate.

### Upcoming Milestones

1. Complete Priority 1 implementation.
2. Demonstrate end-to-end interoperability with Qwen3.
3. Complete the SCRA Conformance Test Suite.
4. Execute the complete regression test suite successfully.
5. Finalize README, Index, Glossary, CHANGELOG, and Release Notes.
6. Publish the SCRA Version 1.0 Release Candidate.

## Phase 1 â€” Priority 1 Implementation

Phase 1 resolves semantic-correctness findings that prevent the Reference
Implementation from conforming to the approved baseline.

Implementation shall proceed in the order defined below unless an approved
dependency change is recorded in this plan.

### Journey Resolver

#### Current Findings

The current resolver:

- resolves physical stops and trip-like entities;
- may retain a `JourneyPattern`;
- does not retain `PointInJourneyPattern`;
- does not retain `stop_sequence`;
- deduplicates by physical stop identity and trip identity;
- may collapse repeated-stop occurrences;
- does not report repeated-stop ambiguity;
- permits suffix-based Relationship matching that may cross source namespaces.

#### Objectives

- Resolve one ordered journey occurrence rather than only one physical stop.
- Preserve repeated visits to the same stop as distinct occurrences.
- Provide downstream services with a stable occurrence discriminator.
- Reject or report ambiguous repeated-stop resolution.
- Preserve source-scoped semantic identity.

#### Implementation Tasks

1. Extend `JourneyResolutionCandidate` with:
   - `boarding_point`;
   - `boarding_stop_sequence`;
   - an occurrence-resolution status or equivalent result.
2. Resolve the preferred path:
   - Trip or ServiceJourney;
   - JourneyPattern;
   - PointInJourneyPattern;
   - PublicTransportStop.
3. Support GTFS StopTime as an equivalent occurrence source where required.
4. Use `stop_sequence` as the primary GTFS occurrence discriminator unless a
   profile defines a more stable equivalent.
5. Change candidate identity from physical stop by trip to ordered occurrence
   by trip.
6. Detect multiple matching occurrences when only `stop_id` is available.
7. Do not silently choose the first or last repeated occurrence.
8. Remove suffix matching from the normative identity path.
9. Evaluate boarding permission and skipped-stop semantics where data is
   available.
10. Add dedicated unit tests before downstream mappers depend on the new
    candidate structure.

#### Dependencies

- `ContextStore` occurrence indexes;
- GTFS Static StopTime and JourneyPattern mappings;
- PointInJourneyPattern identity rules;
- existing arrival-estimation services.

#### Expected Deliverables

- updated Journey Resolver models and implementation;
- occurrence-resolution helper functions;
- repeated-stop ambiguity result or exception;
- dedicated Journey Resolver unit tests;
- compatibility notes for existing callers.

#### Exit Criteria

- two visits to the same stop in one journey resolve as distinct occurrences;
- `stop_sequence` is retained;
- PointInJourneyPattern is retained when available;
- ambiguous stop-only resolution is reported;
- source namespaces are not crossed by implicit suffix matching;
- existing compatible tests pass;
- new Journey Resolver tests pass.

### PassingEvent Mapper

#### Current Findings

The current mapper:

- creates `PassingEvent`;
- supports a fixed arrival event;
- does not retain an ordered occurrence discriminator;
- may emit schedule-only time as `predictedTime`;
- does not support explicit `observedTime`;
- may emit `predictionConfidence` without a predicted value;
- passes through `minutesUntilArrival`;
- depends on a current calculation that rounds partial positive minutes upward.

#### Objectives

- Produce stable event identity from monitored execution, occurrence, and
  `eventType`.
- Support arrival and departure events.
- Classify scheduled, predicted, and observed times explicitly.
- Enforce conditional Property requirements.
- implement the normative `minutesUntilArrival` calculation.

#### Implementation Tasks

1. Extend mapper input with:
   - monitored journey identity;
   - PointInJourneyPattern or `stop_sequence`;
   - `eventType`;
   - explicit time semantics.
2. Construct identity from:
   - `MonitoredVehicleJourney`;
   - ordered occurrence;
   - `eventType`.
3. Support at least `arrival` and `departure`.
4. Emit `scheduledTime` only for the applicable static baseline.
5. Emit `predictedTime` only for predicted operational values.
6. Emit `observedTime` only with explicit operational evidence.
7. Emit `predictionConfidence` only when a predicted value is present.
8. Produce normative `delay` with signed whole seconds and `unitCode = SEC`.
9. Correct `minutesUntilArrival` to:
   - use `predictedTime` when available;
   - otherwise use `scheduledTime`;
   - truncate positive partial minutes toward zero;
   - clamp past values to zero;
   - include the calculation `referenceTime`.
10. Preserve the accessibility rule that whole minutes are not sufficient for
    close-range guidance.

#### Dependencies

- Journey Resolver occurrence output;
- monitored-execution identity;
- Passing Time Estimator;
- TripUpdate mapping changes.

#### Expected Deliverables

- updated PassingEvent input and mapper;
- arrival/departure identity support;
- explicit temporal classification;
- corrected minute calculation;
- new unit and integration tests.

#### Exit Criteria

- repeated-stop event identities are distinct;
- arrival and departure identities are distinct;
- schedule-only output does not contain `predictedTime`;
- `observedTime` is emitted only from explicit evidence;
- confidence conditions are enforced;
- 59 seconds produces `0`;
- 119 seconds produces `1`;
- past arrival produces `0`;
- all related tests pass.

### TripUpdate Mapper

#### Current Findings

The current mapper:

- maps TripUpdate to a static `Trip`;
- preserves GTFS Realtime source fields;
- embeds StopTimeUpdates as a structured Property;
- does not generate `MonitoredVehicleJourney`;
- does not generate `PassingEvent`;
- does not include service date or start-time discrimination in execution
  identity;
- may reject distinct executions sharing one trip identifier as duplicates.

#### Objectives

- Separate static journey identity from monitored execution identity.
- Generate or support `MonitoredVehicleJourney`.
- Generate distinct PassingEvents from StopTimeUpdate arrival and departure.
- Resolve stop occurrence using `stop_sequence`.
- Preserve GTFS source information without embedding normative operational
  events in one static Trip Property.

#### Implementation Tasks

1. Define monitored-execution identity using:
   - source;
   - `trip_id`;
   - `start_date` or resolved service date;
   - `start_time` or another discriminator when required.
2. Map TripDescriptor to the applicable `MonitoredVehicleJourney`.
3. Relate it to the static or dated journey.
4. Update duplicate detection to use monitored-execution identity.
5. Resolve each StopTimeUpdate to PointInJourneyPattern or equivalent
   occurrence.
6. Generate separate arrival and departure PassingEvents.
7. Map StopTimeEvent time to `predictedTime` unless explicit semantics support
   `observedTime`.
8. Map signed delay to the normative delay representation.
9. Preserve uncertainty without unsafe automatic conversion to
   `predictionConfidence`.
10. Map TripUpdate timestamp to the applicable observation/report semantics.
11. Apply `SKIPPED`, `NO_DATA`, cancellation, and related schedule semantics.

#### Dependencies

- Journey Resolver occurrence model;
- PassingEvent Mapper;
- monitored-execution identity utilities;
- static GTFS context.

#### Expected Deliverables

- monitored journey mapper or builder;
- PassingEvent output from TripUpdate;
- execution-aware duplicate handling;
- StopTimeUpdate occurrence resolution;
- expanded unit and integration tests.

#### Exit Criteria

- different monitored executions do not collide;
- TripUpdate produces or updates a `MonitoredVehicleJourney`;
- arrival and departure produce distinct PassingEvents;
- repeated stops resolve through `stop_sequence`;
- ambiguous stop-only updates are rejected or reported;
- schedule relationships affect operational behavior;
- all related tests pass.

### VehiclePosition Mapper

#### Current Findings

The current mapper:

- creates source-scoped Vehicle entities;
- maps position, speed, bearing, current stop, current stop sequence, occupancy,
  and observation time;
- preserves TripDescriptor start date and start time;
- relates Vehicle directly to a static Trip;
- does not resolve the applicable `MonitoredVehicleJourney`;
- does not resolve current stop sequence to PointInJourneyPattern.

#### Objectives

- Retain the current strong vehicle-state mapping.
- Resolve VehiclePosition to the applicable monitored execution.
- Resolve current stop sequence to the ordered journey occurrence.
- preserve observation-time semantics.

#### Implementation Tasks

1. Reuse the monitored-execution identity defined for TripUpdate.
2. Resolve the applicable `MonitoredVehicleJourney`.
3. Preserve the PhysicalVehicle identity separately from the monitored
   execution.
4. Resolve `current_stop_sequence` and `stop_id` to PointInJourneyPattern or
   equivalent occurrence.
5. Preserve VehiclePosition timestamp as observation time.
6. Ensure serialization exposes the applicable SCRA observation semantics.
7. Add repeated-stop and monitored-execution integration tests.

#### Dependencies

- monitored-execution identity;
- Journey Resolver occurrence model;
- presentation and serialization layer.

#### Expected Deliverables

- monitored-execution Relationship or resolution output;
- current occurrence resolution;
- updated unit and integration tests;
- no regression in location, speed, bearing, or occupancy mapping.

#### Exit Criteria

- Vehicle state resolves to the correct monitored execution;
- current stop sequence resolves to the correct occurrence;
- repeated stops do not collapse;
- source-scoped vehicle identity remains stable;
- observation time remains offset-aware;
- existing and new tests pass.

## Phase 2 â€” Conformance Test Suite

### Objectives

- Demonstrate that resolved Priority 1 findings satisfy the normative
  requirements.
- Provide regression protection for Version 1.0 RC.
- establish traceability from requirement to baseline finding to test.

### Requirement Traceability

Each test shall identify:

- the normative specification section or vocabulary term;
- the related baseline finding;
- the implementation component;
- the expected conformant outcome.

A coverage matrix shall be maintained for:

- PassingEvent identity;
- MonitoredVehicleJourney identity;
- PointInJourneyPattern occurrence;
- repeated stop handling;
- delay;
- prediction confidence;
- minutes until arrival;
- GTFS Static mapping;
- GTFS Realtime mapping;
- JSON-LD context behavior.

### Unit Tests

At minimum:

- Journey Resolver occurrence tests;
- PassingEvent identity tests;
- temporal-classification tests;
- delay tests;
- prediction-confidence tests;
- minutes-until-arrival boundary tests;
- monitored-execution identity tests;
- VehiclePosition occurrence tests.

### Integration Tests

At minimum:

- GTFS Static trip and StopTime to occurrence mapping;
- TripUpdate to MonitoredVehicleJourney and PassingEvent;
- VehiclePosition to MonitoredVehicleJourney;
- repeated-stop end-to-end behavior;
- arrival/departure separation;
- source-namespace isolation.

### Regression Tests

All existing unit and integration tests shall continue to pass unless a test
encodes behavior that conflicts with the approved baseline.

Any changed test expectation shall be documented and traced to the approved
finding.

### JSON-LD Validation

Tests shall verify:

- context JSON validity;
- compact-term registration;
- expected IRI expansion;
- Relationship object identifiers;
- normative unit codes;
- detection of unregistered terms.

### Expected Deliverables

- `tests/conformance/`;
- reusable fixtures;
- requirement traceability matrix;
- JSON-LD validation tests;
- documented test command.

### Exit Criteria

- every Priority 1 finding has at least one conformance test;
- all conformance tests pass;
- all non-conflicting regression tests pass;
- the traceability matrix has no unresolved Priority 1 row;
- JSON-LD validation passes.

## Phase 3 â€” Full Test Execution

### Objectives

Execute all required quality gates together.

### Test Set

- existing pytest suite;
- SCRA Conformance Test Suite;
- GTFS Static mapping tests;
- GTFS Realtime mapping tests;
- JSON-LD validation;
- worked-example validation;
- Qwen3 interoperability demonstration.

### Exit Criteria

- pytest passes;
- conformance tests pass;
- JSON-LD validation passes;
- worked examples validate;
- Qwen3 obtains mobility information through Mobility MCP successfully;
- failures are resolved or explicitly accepted and documented.

## Phase 4 â€” Documentation and Publication Preparation

### README

Update:

- project purpose;
- normative scope;
- repository structure;
- semantic model overview;
- GTFS mapping;
- examples;
- Reference Implementation;
- conformance testing;
- Qwen3 interoperability demonstration;
- version and status;
- license.

### Index

Update or create indexes for:

- vocabulary;
- semantic models;
- mappings;
- examples;
- conformance documents;
- releases.

### Glossary

Include:

- entity types;
- Relationships;
- Properties;
- temporal terms;
- identity terms;
- mapping terms;
- conformance terms.

### CHANGELOG

Record Version 1.0 RC changes under:

- Added;
- Changed;
- Clarified;
- Fixed;
- Deprecated, if applicable.

### Release Notes

Include:

- Version 1.0 RC scope;
- normative vocabulary;
- semantic models;
- GTFS mappings;
- examples;
- implementation conformance;
- Qwen3 demonstration;
- known limitations;
- future work.

### Cross References

Verify links among:

- vocabulary;
- semantic models;
- mappings;
- examples;
- context;
- conformance review;
- remediation plan;
- tests;
- release notes.

### Exit Criteria

- documentation is internally consistent;
- links are valid;
- implementation status matches test results;
- known limitations are explicit;
- publication review is complete.

## Risk Management

### Identity Migration

Changing PassingEvent or MonitoredVehicleJourney identity may invalidate
existing cached URIs.

Mitigation:

- document migration behavior;
- isolate identity construction;
- add deterministic migration and regression tests.

### Repeated-Stop Regression

Occurrence-aware resolution may change current arrival results.

Mitigation:

- add repeated-stop fixtures before modifying downstream code;
- preserve current behavior for unambiguous single-occurrence trips.

### GTFS Provider Variation

Providers may omit `start_date`, `start_time`, `stop_sequence`, or stable
vehicle identifiers.

Mitigation:

- define profile-controlled fallback behavior;
- report ambiguity rather than invent identity;
- keep source provenance.

### JSON-LD Drift

Implementation terms may diverge from `mobility.jsonld`.

Mitigation:

- add automated context validation;
- reject unregistered normative compact terms.

### Qwen3 Integration Delay

Model-client integration may expose MCP transport or prompt-interface issues
unrelated to the semantic model.

Mitigation:

- use a minimal end-to-end demonstration first;
- keep semantic and transport failures separately diagnosable;
- record blockers in this plan.

### Schedule Pressure

The desire to connect Qwen3 quickly may encourage bypassing Priority 1
identity or occurrence fixes.

Mitigation:

- complete only the minimum Priority 1 set required for a semantically valid
  demonstration;
- do not treat a non-conformant demonstration as Version 1.0 RC evidence.

## Acceptance Criteria

A remediation item is accepted only when:

- the implementation change is complete;
- the applicable code review is complete;
- related unit tests pass;
- related conformance tests pass;
- regression tests pass;
- documentation is synchronized;
- the baseline finding is marked resolved or explicitly accepted.

The Qwen3 interoperability milestone is accepted when:

- Qwen3 connects to the Mobility MCP Reference Implementation;
- the client discovers and invokes the required Mobility MCP capabilities;
- the result uses the applicable SCRA semantic model;
- repeated-stop and monitored-execution identity are not bypassed;
- the demonstration is reproducible and documented.

## Version 1.0 RC Release Criteria

The Version 1.0 Release Candidate may be published only when:

- the Baseline Review is approved;
- this execution plan is approved;
- all Priority 1 findings are resolved;
- unresolved Priority 2 and Priority 3 items are explicitly accepted and
  documented;
- the SCRA Conformance Test Suite passes;
- the complete pytest suite passes;
- GTFS mapping validation passes;
- JSON-LD validation passes;
- worked examples are verified;
- Qwen3 interoperability has been demonstrated;
- README is updated;
- indexes are updated;
- Glossary is completed;
- CHANGELOG is completed;
- Release Notes are completed;
- known limitations are documented.

## Execution Policy

This remediation plan shall be used as the working execution plan for the
SCRA Version 1.0 Release Candidate.

At the beginning of each implementation session, work shall resume by
reviewing:

1. `reference-implementation-review.md`;
2. `reference-implementation-remediation-plan.md`.

Implementation work shall follow the priorities defined in this document.

Each Priority 1 change shall be traceable to an approved conformance finding
and shall be verified through the SCRA Conformance Test Suite.

New findings shall be recorded in the Reference Implementation Conformance
Review before changes to this execution plan are approved.

Implementation work shall not redefine normative SCRA semantics without an
approved update to the applicable baseline documents.

## Approval

This document becomes an approved Version 1.0 RC baseline execution plan only
after explicit project review and approval.

| Field | Value |
|---|---|
| Prepared | Pending |
| Reviewed | Pending |
| Approved | Pending |
| Baseline version | Version 1.0 RC |
| Revision | 1 |

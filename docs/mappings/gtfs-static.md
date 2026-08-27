# GTFS Static Mapping

## Status

Normative

---

## 1. Purpose

This document defines the semantic correspondence between the General
Transit Feed Specification Schedule data model, referred to here as
GTFS Static, and the canonical semantic models defined by SCRA Schema.

The purpose of this specification is to preserve the meaning of GTFS
Schedule information while making that information available through a
source-independent semantic model.

This specification does not define an NGSI-LD representation, JSON-LD
serialization, software implementation, API, or data-processing pipeline.

---

## 2. Scope

This specification covers the GTFS Static concepts required for the initial
SCRA public transport profile and Mobility MCP proof of concept.

The primary source files covered are:

- `agency.txt`
- `routes.txt`
- `stops.txt`
- `trips.txt`
- `stop_times.txt`
- `calendar.txt`
- `calendar_dates.txt`
- `shapes.txt`

Other GTFS files may be mapped by later versions of this specification.

---

## 3. Mapping Principles

### 3.1 Semantic Correspondence

GTFS records shall be interpreted according to their source semantics and
mapped to corresponding SCRA semantic concepts.

A GTFS table or record shall not automatically become one SCRA entity.

A single GTFS record may contribute information to multiple semantic
concepts.

A single semantic concept may require information from multiple GTFS files.

---

### 3.2 Source Independence

GTFS-specific table names, field names, and nesting structures shall not
become part of the canonical semantic definition unless they also represent
the intended domain meaning.

GTFS identifiers may be preserved for traceability, but they shall be
interpreted within the scope of the source dataset or source authority.

---

### 3.3 No Unjustified Semantics

A mapping shall not introduce semantic certainty that is absent from the
source data.

Where GTFS does not explicitly distinguish two SCRA concepts, the mapping
shall document the inference used or leave the distinction unresolved.

---

### 3.4 N-to-M Mapping

Mappings are not required to be one-to-one.

For example:

- one `trips.txt` record contributes to a ServiceJourney;
- its ordered `stop_times.txt` records contribute to a JourneyPattern,
  PointInJourneyPattern instances, and scheduled timing information;
- its `shape_id` may contribute geometry to the JourneyPattern; and
- its `service_id` connects the ServiceJourney to calendar semantics.

---

## 4. Core Correspondence

| GTFS source | SCRA semantic concept | Mapping role |
|---|---|---|
| `agency.txt` | Transport operator or authority | Identifies the organisation providing or representing service |
| `routes.txt` | PublicTransportRoute | Defines the passenger-facing grouping of related journeys |
| `stops.txt` | PublicTransportStop or TransportStation | Defines transport locations according to `location_type` and related fields |
| `trips.txt` | ServiceJourney | Defines a recurring scheduled passenger-carrying journey |
| ordered `stop_times.txt` records | JourneyPattern and PointInJourneyPattern | Defines the ordered operational stopping pattern used by a journey |
| `stop_times.txt` arrival and departure fields | Timetabled passing information | Defines planned arrival and departure timing at a PointInJourneyPattern |
| `calendar.txt` | ServiceCalendar and DayType inputs | Defines recurring weekly availability and validity intervals |
| `calendar_dates.txt` | ServiceCalendar and OperatingDay inputs | Adds, removes, or explicitly enumerates applicable dates |
| resolved service date | OperatingDay | Provides the dated operational context |
| `shapes.txt` | JourneyPattern geometry | Describes the geographical path followed by a journey |
| `trips.txt.shape_id` | JourneyPattern geometry reference | Associates a scheduled journey with source path geometry |

The table defines semantic correspondence, not a required physical
serialization.

---

## 5. Agency Mapping

### 5.1 Source Meaning

An `agency.txt` record describes a transit agency whose services are
represented in the GTFS dataset.

### 5.2 Semantic Interpretation

The record shall be mapped to the semantic concept representing the
organisation that provides, manages, or publishes the transport service.

The mapping shall preserve distinctions among:

- service operator;
- transport authority;
- data publisher; and
- other organisational roles,

where the source dataset provides sufficient information.

The mapping shall not assume that every agency is simultaneously the legal
owner, operational provider, transport authority, and data publisher.

### 5.3 Identifier Scope

`agency_id` is a source identifier.

Its semantic scope shall include the GTFS source or another explicitly
defined authority namespace.

---

## 6. Route Mapping

### 6.1 Source Meaning

A `routes.txt` record represents a group of trips presented to passengers as
one service.

### 6.2 Semantic Target

Each applicable `routes.txt` record normally maps to one
PublicTransportRoute.

### 6.3 Relationships

A PublicTransportRoute may be related to:

- the operator or authority identified through `agency_id`;
- multiple ServiceJourneys;
- one transport mode or route type; and
- multiple JourneyPatterns.

### 6.4 Route and Pattern Distinction

A PublicTransportRoute shall not be interpreted as the detailed ordered stop
sequence or exact travelled geometry.

Different JourneyPatterns may belong to the same PublicTransportRoute.

Changes in stop sequence, stopping behaviour, or detailed path may therefore
create a different JourneyPattern without necessarily creating a different
PublicTransportRoute.

---

## 7. Stop and Station Mapping

### 7.1 Source Meaning

`stops.txt` may describe several types of transport location, including
stops, platforms, stations, entrances, generic nodes, and boarding areas.

### 7.2 Semantic Target

The semantic target shall be selected using the GTFS location semantics,
including `location_type`, `parent_station`, and related fields.

Typical correspondence includes:

| GTFS location | SCRA semantic interpretation |
|---|---|
| stop or platform | PublicTransportStop |
| station | TransportStation |
| entrance or exit | Access-related location associated with TransportStation |
| boarding area | Sub-location associated with a PublicTransportStop |
| generic node | Internal navigation or interchange location |

### 7.3 Parent Relationships

`parent_station` shall be mapped as a semantic relationship between the
contained location and its containing station or parent location.

It shall not be treated merely as a copied source identifier.

### 7.4 Identity

`stop_id` is a source identifier and shall not automatically be treated as a
globally unique semantic identifier.

---

## 8. ServiceJourney Mapping

### 8.1 Source Meaning

A `trips.txt` record describes one scheduled trip associated with a route and
a set of service dates.

### 8.2 Semantic Target

Each applicable `trips.txt` record normally maps to one ServiceJourney.

### 8.3 Field Correspondence

| GTFS field | Semantic interpretation |
|---|---|
| `trip_id` | Source identifier for ServiceJourney |
| `route_id` | Relationship to PublicTransportRoute |
| `service_id` | Reference to recurring calendar applicability |
| `direction_id` | Direction classification |
| `trip_headsign` | Passenger-facing destination information |
| `trip_short_name` | Passenger-facing journey designation |
| `shape_id` | Source reference to JourneyPattern geometry |
| `block_id` | Operational block or vehicle-work reference, not ServiceJourney identity |
| `wheelchair_accessible` | Planned accessibility information |
| `bikes_allowed` | Planned bicycle-access information |

### 8.4 Identity

A `trip_id` is unique only within the applicable GTFS dataset.

The mapping shall define:

- source authority;
- dataset or feed scope;
- versioning policy; and
- treatment of timetable revisions.

A change in source identifier does not by itself prove that the semantic
ServiceJourney has changed.

---

## 9. JourneyPattern Mapping

### 9.1 Source Construction

GTFS does not explicitly define a JourneyPattern entity.

A JourneyPattern shall be derived from the planned operational structure of
one or more ServiceJourneys.

Relevant source information includes:

- ordered `stop_times.txt` records;
- `stop_sequence`;
- `stop_id`;
- pickup and drop-off behaviour;
- timepoint information;
- continuous pickup or drop-off behaviour;
- `shape_id`; and
- geometry in `shapes.txt`.

### 9.2 Pattern Reuse

Multiple ServiceJourneys may map to the same JourneyPattern when they share
the same relevant ordered operational pattern.

Differences in scheduled times alone do not require different
JourneyPatterns.

Differences in any of the following may require distinct JourneyPatterns:

- ordered stop sequence;
- repeated-stop structure;
- stopping behaviour;
- boarding or alighting conditions;
- operational path; or
- another pattern-defining characteristic.

The precise equivalence rules shall be defined by the mapping profile used
by the implementation.

### 9.3 Shape Geometry

`shapes.txt` contributes geometry to a JourneyPattern but does not by itself
define the complete JourneyPattern.

A shape is an ordered geographical path.

It does not necessarily define:

- the stop sequence;
- boarding or alighting conditions;
- timing points; or
- passenger-service meaning.

---

## 10. PointInJourneyPattern Mapping

### 10.1 Source Construction

Each ordered `stop_times.txt` record normally contributes one
PointInJourneyPattern to the JourneyPattern associated with the trip.

### 10.2 Field Correspondence

| GTFS field | Semantic interpretation |
|---|---|
| `trip_id` | Source journey context used to identify or derive JourneyPattern |
| `stop_sequence` | Ordering within JourneyPattern |
| `stop_id` | Relationship to PublicTransportStop |
| `pickup_type` | Boarding condition |
| `drop_off_type` | Alighting condition |
| `continuous_pickup` | Continuous boarding condition |
| `continuous_drop_off` | Continuous alighting condition |
| `timepoint` | Timing-point classification |
| `stop_headsign` | Point-specific passenger destination information |
| `shape_dist_traveled` | Position along source geometry |

### 10.3 Repeated Stops

The same PublicTransportStop may occur more than once in one JourneyPattern.

Each occurrence shall be represented by a distinct PointInJourneyPattern.

The semantic identity of the point therefore depends on the JourneyPattern
context and its position or equivalent pattern-specific identity, not only
on `stop_id`.

---

## 11. Timetabled Passing Information

### 11.1 Source Meaning

`stop_times.txt` provides scheduled arrival and departure information for a
trip at an ordered stop occurrence.

### 11.2 Semantic Interpretation

The arrival and departure values shall be interpreted as timetabled passing
information associated with:

- one ServiceJourney; and
- one PointInJourneyPattern.

Static scheduled timing shall not be mapped to MonitoredVehicleJourney,
EstimatedPassingTime, ObservedPassingTime, or other real-time concepts.

### 11.3 Arrival and Departure

Arrival and departure are distinct temporal aspects.

The absence of one value shall not cause an implementation to invent a value
unless the GTFS specification or an explicitly documented mapping rule
supports that derivation.

### 11.4 Times Beyond Midnight

GTFS times may exceed `24:00:00`.

Such values shall be interpreted within the GTFS service-day convention and
shall not be truncated to a civil-clock time without preserving the
operational-day context.

### 11.5 Deferred Model Detail

The detailed normative model for:

- TimetabledPassingTime;
- TargetPassingTime;
- EstimatedPassingTime;
- ObservedPassingTime; and
- Call-related views

will be completed separately.

Until then, the mapping shall preserve the distinction between static
timetabled values and real-time estimated or observed values.

---

## 12. Calendar Mapping

### 12.1 Combined Interpretation

`calendar.txt` and `calendar_dates.txt` shall be interpreted together.

A `service_id` identifies a set of dates on which associated service is
available.

It shall not automatically be treated as identical to one ServiceCalendar,
DayType, or OperatingDay.

### 12.2 Weekly Rules

The weekday flags and validity interval in `calendar.txt` contribute:

- recurring calendar rules;
- one or more possible DayType classifications; and
- the validity scope of the applicable ServiceCalendar arrangement.

### 12.3 Date Exceptions

A `calendar_dates.txt` record contributes an explicit date assignment.

Depending on `exception_type`, the date is added to or removed from the
applicable service-date set.

Where `calendar.txt` is absent, `calendar_dates.txt` may enumerate the
complete set of service dates.

### 12.4 OperatingDay Resolution

Each resolved service date may correspond to an OperatingDay.

The mapping shall define:

- operational authority;
- timezone;
- operational-day boundary;
- interpretation of times greater than `24:00:00`; and
- source scope.

A civil date alone may be insufficient to define the complete temporal extent
or global identity of an OperatingDay.

### 12.5 Relationship to ServiceJourney

`trips.txt.service_id` relates a ServiceJourney to its recurring calendar
applicability.

It does not directly identify one DatedVehicleJourney.

DatedVehicleJourneys are produced when the recurring service definition is
resolved for particular OperatingDays.

---

## 13. DatedVehicleJourney Derivation

GTFS Static primarily defines recurring scheduled service.

A DatedVehicleJourney may be derived by combining:

- a ServiceJourney;
- a resolved OperatingDay; and
- any applicable dated operational modification.

The mere presence of one `trips.txt` record does not define one
DatedVehicleJourney independently of an OperatingDay.

---

## 14. Traceability

A conforming mapping should preserve sufficient source provenance to trace a
semantic instance to the source records from which it was derived.

Relevant provenance may include:

- source feed identifier;
- source authority;
- dataset version;
- retrieval or publication time;
- source file;
- source record identifier; and
- mapping profile version.

Source provenance is distinct from semantic identity.

---

## 15. Conformance

A GTFS Static mapping conforms to this specification when it:

- preserves the source meaning defined by GTFS;
- maps source information to the corresponding SCRA semantic concepts;
- distinguishes route, journey, journey pattern, and ordered pattern point;
- interprets `calendar.txt` and `calendar_dates.txt` together;
- preserves the operational-day semantics of times beyond midnight;
- does not treat source identifiers as globally unique without an explicit
  namespace;
- distinguishes static timetabled information from real-time information;
- does not introduce unsupported semantic certainty; and
- preserves sufficient provenance for traceability.

---

## 16. References

- GTFS Schedule Reference:
  https://gtfs.org/documentation/schedule/reference/

- GTFS Schedule Best Practices:
  https://gtfs.org/documentation/schedule/schedule-best-practices/

- Transmodel:
  https://www.transmodel-cen.eu/

- SCRA Semantic Modeling Principles:
  ../specifications/semantic-modeling-principles.md

- SCRA Semantic Models:
  ../models/index.md

---

<!-- SCRA-PHASE4-GTFS-AGENCY-ORGANISATION -->

### Organisation and operator role normalization

SCRA maps the organisational identity represented by GTFS `agency.txt` to the
normative `Organisation` semantic class.

The mapping SHALL preserve organisational identity separately from
organisational role.

A GTFS agency may act as:

- service operator;
- transport authority;
- data publisher;
- vehicle owner or manager; or
- another organisational role supported by the source.

The mapping SHALL NOT assume that every agency performs all such roles.

Where the source semantics establish that an Organisation operates a
ServiceJourney or PhysicalVehicle, the normative SCRA `operator` relationship
may be used.

`agency_id` remains a source identifier and SHALL be scoped by the applicable
GTFS dataset, source, or authority namespace.

The existence of an `agency_id` reference SHALL NOT by itself create an
`operator` relationship unless the source semantics justify that role.

© SCRA Project

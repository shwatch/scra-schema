# NGSI-LD Mapping

## Status

Normative

---

## 1. Purpose

This document defines how canonical semantic concepts defined by SCRA Schema
are represented using the NGSI-LD information model.

The purpose of this specification is to provide a consistent NGSI-LD
representation without making the underlying SCRA semantic models dependent
on NGSI-LD.

SCRA semantic models define meaning.

This specification defines one technology-specific representation of that
meaning.

---

## 2. Scope

This specification covers the NGSI-LD representation required for the
initial SCRA public transport profile and Mobility MCP proof of concept.

It defines principles for mapping:

- semantic concepts to NGSI-LD Entity Types;
- semantic identity to NGSI-LD Entity identifiers;
- semantic characteristics to NGSI-LD Properties;
- semantic relationships to NGSI-LD Relationships;
- geographic state to NGSI-LD GeoProperties;
- temporal and provenance information to NGSI-LD attributes; and
- SCRA vocabulary terms to JSON-LD identifiers.

This specification does not define:

- a particular NGSI-LD Broker;
- an HTTP deployment architecture;
- an ingestion pipeline;
- database storage;
- access control;
- MCP Tools or Resources;
- GTFS parsing algorithms; or
- source-specific identifier construction rules.

---

## 3. Mapping Principles

### 3.1 Semantic Models Remain Authoritative

The normative meaning and semantic identity of each concept are defined by
the applicable SCRA semantic model.

The NGSI-LD representation shall not redefine that meaning.

Where an implementation detail conflicts with a semantic model, the semantic
model remains authoritative.

---

### 3.2 Entity Representation

A semantic concept that has:

- independent semantic identity;
- an independently addressable lifecycle; or
- relationships that must be referenced independently

should normally be represented as an NGSI-LD Entity.

A value or structure shall not be promoted to an Entity solely because the
source format represents it as a separate record or message.

Conversely, a semantic concept shall not be embedded as an anonymous value
when its independent identity and relationships must be preserved.

---

### 3.3 Property Representation

A semantic characteristic shall normally be represented as an NGSI-LD
Property when its value:

- describes the Entity;
- is not primarily a reference to another independently identified Entity;
  and
- can be interpreted without becoming a separate semantic subject.

Examples include:

- names;
- labels;
- sequence values;
- operational status;
- speed;
- bearing;
- delay;
- timestamps; and
- source identifiers retained for traceability.

---

### 3.4 Relationship Representation

A semantic relationship between independently identified concepts shall be
represented as an NGSI-LD Relationship.

Examples include:

- ServiceJourney to PublicTransportRoute;
- ServiceJourney to JourneyPattern;
- DatedVehicleJourney to ServiceJourney;
- DatedVehicleJourney to OperatingDay;
- MonitoredVehicleJourney to DatedVehicleJourney;
- MonitoredVehicleJourney to PhysicalVehicle;
- PointInJourneyPattern to JourneyPattern; and
- PointInJourneyPattern to PublicTransportStop.

A source identifier shall be resolved to the target Entity identifier before
being used as the object of an NGSI-LD Relationship whenever the target
semantic Entity is known.

---

### 3.5 GeoProperty Representation

A geographic location or geometry shall normally be represented as an
NGSI-LD GeoProperty when the value represents GeoJSON-compatible spatial
information.

Typical examples include:

- the observed location of a PhysicalVehicle;
- the location of a PublicTransportStop;
- the location or extent of a TransportStation; and
- the geometry associated with a JourneyPattern.

The semantic scope of the geometry shall be explicit.

For example, an observed vehicle location and a planned journey geometry are
different semantic properties and shall not be conflated.

---

### 3.6 N-to-M Mapping

The mapping from source information to SCRA Entities and from SCRA concepts
to NGSI-LD attributes is not required to be one-to-one.

One source message may update multiple NGSI-LD Entities.

One NGSI-LD Entity may be constructed or updated using information from
multiple source messages.

For example, GTFS-Realtime VehiclePosition information may update:

- the location and physical state of a PhysicalVehicle; and
- the progress and operational state of a MonitoredVehicleJourney.

---

### 3.7 No Source-Format Nesting

Source-format nesting shall not be reproduced merely for structural
convenience.

For example:

- a GTFS-Realtime TripUpdate shall not be stored as an opaque nested
  property when its content maps to DatedVehicleJourney,
  MonitoredVehicleJourney, and passing-time semantics;
- a VehicleDescriptor shall not remain an anonymous nested object when it
  identifies a PhysicalVehicle; and
- a source stop identifier shall not replace a Relationship to a
  PublicTransportStop where the target Entity can be resolved.

Opaque source payloads may be retained separately for provenance or
diagnostics, but they do not replace the canonical semantic representation.

---

## 4. Entity Types

The following SCRA semantic concepts normally map to NGSI-LD Entity Types.

| SCRA semantic concept | NGSI-LD Entity Type |
|---|---|
| PublicTransportRoute | `PublicTransportRoute` |
| PublicTransportStop | `PublicTransportStop` |
| TransportStation | `TransportStation` |
| ServiceJourney | `ServiceJourney` |
| JourneyPattern | `JourneyPattern` |
| PointInJourneyPattern | `PointInJourneyPattern` |
| ServiceCalendar | `ServiceCalendar` |
| DayType | `DayType` |
| OperatingDay | `OperatingDay` |
| DatedVehicleJourney | `DatedVehicleJourney` |
| MonitoredVehicleJourney | `MonitoredVehicleJourney` |
| PhysicalVehicle | `PhysicalVehicle` |

Passing-time concepts will be added when their normative semantic models are
completed.

The use of these compact type names requires corresponding vocabulary
definitions in the SCRA JSON-LD context.

---

## 5. Entity Identifiers

### 5.1 URI Requirement

Each NGSI-LD Entity identifier shall be a URI.

The URI shall identify the semantic instance rather than merely reproduce a
source record identifier without context.

### 5.2 Semantic Identity

Entity identifier construction shall follow the `Identity` section of the
applicable SCRA semantic model.

The following shall not independently determine semantic identity:

- GTFS `trip_id`;
- GTFS `stop_id`;
- GTFS `route_id`;
- GTFS-Realtime `FeedEntity.id`;
- a database primary key;
- a protobuf message identifier;
- a source filename; or
- a retrieval timestamp.

These values may contribute to identifier construction when combined with
the required source authority, dataset scope, operating-day context, or
other semantic identity components.

### 5.3 Stability

An Entity identifier should remain stable while the semantic identity of the
concept remains unchanged.

Changing operational values such as:

- delay;
- location;
- speed;
- occupancy;
- assigned vehicle;
- prediction; or
- monitoring timestamp

shall not result in a new Entity identifier unless the underlying semantic
instance has changed.

### 5.4 Source Authority

Identifier construction shall include or otherwise preserve the authority
within which source identifiers are unique.

A conforming profile shall define:

- the identifier namespace;
- source authority;
- source-system scope;
- encoding and normalization rules;
- operating-day treatment where applicable; and
- collision handling.

### 5.5 Recommended Pattern

An implementation may use a URN pattern such as:

    urn:ngsi-ld:<EntityType>:<Authority>:<LocalIdentity>

This is an implementation pattern and not a complete semantic identity rule.

The exact construction shall be defined by the applicable SCRA NGSI-LD
profile.

---

## 6. Vocabulary and JSON-LD Context

### 6.1 Vocabulary Identifiers

Every SCRA Entity Type, Property, and Relationship used in NGSI-LD shall
have a globally identifiable vocabulary IRI.

Compact terms shall be expanded through a JSON-LD `@context`.

### 6.2 SCRA Namespace

SCRA vocabulary identifiers should use the authoritative SCRA Schema domain.

The initial namespace shall be defined under:

    https://scra-schema.org/

The final vocabulary paths and versioning policy will be specified
separately.

Illustrative forms include:

    https://scra-schema.org/vocab/PublicTransportRoute
    https://scra-schema.org/vocab/operatedByVehicle
    https://scra-schema.org/vocab/serviceJourney

These examples do not become normative vocabulary identifiers until the
SCRA context and vocabulary specifications are published.

### 6.3 User Context

The SCRA JSON-LD context shall map:

- each compact Entity Type name to its Type IRI;
- each Property name to its Property IRI; and
- each Relationship name to its Relationship IRI.

Relationship terms shall expand as identifiers so that their objects are
interpreted as Entity URIs.

### 6.4 Existing Vocabulary Reuse

Where an existing international vocabulary defines a term with the required
meaning, that vocabulary term should be reused.

SCRA-specific vocabulary terms shall be created only when:

- no suitable existing term exists;
- existing terms have incompatible meanings; or
- a harmonized SCRA term is required to resolve ambiguity.

The selected vocabulary origin shall be documented.

---

## 7. Common Relationships

The following relationship names are candidates for the initial SCRA
NGSI-LD profile.

Their final names and IRIs shall be confirmed in the vocabulary
specification.

| Subject Entity | Candidate Relationship | Object Entity |
|---|---|---|
| ServiceJourney | `route` | PublicTransportRoute |
| ServiceJourney | `journeyPattern` | JourneyPattern |
| ServiceJourney | `operator` | Organisation |
| ServiceJourney | `serviceCalendar` | ServiceCalendar |
| ServiceJourney | `dayType` | DayType |
| JourneyPattern | `route` | PublicTransportRoute |
| PointInJourneyPattern | `journeyPattern` | JourneyPattern |
| PointInJourneyPattern | `stop` | PublicTransportStop |
| DatedVehicleJourney | `serviceJourney` | ServiceJourney |
| DatedVehicleJourney | `operatingDay` | OperatingDay |
| DatedVehicleJourney | `assignedVehicle` | PhysicalVehicle |
| MonitoredVehicleJourney | `datedVehicleJourney` | DatedVehicleJourney |
| MonitoredVehicleJourney | `operatedByVehicle` | PhysicalVehicle |
| MonitoredVehicleJourney | `currentPoint` | PointInJourneyPattern |
| MonitoredVehicleJourney | `currentStop` | PublicTransportStop |
| PhysicalVehicle | `operator` | Organisation |
| PublicTransportStop | `parentStation` | TransportStation |
| OperatingDay | `dayType` | DayType |
| OperatingDay | `serviceCalendar` | ServiceCalendar |

This table defines candidate semantic relationships for the profile.

It does not establish final vocabulary IRIs or mandatory cardinalities.

---

## 8. Common Properties

The following property groups are candidates for the initial profile.

### 8.1 Identification and Labels

Typical properties include:

- `name`;
- `label`;
- `description`;
- `sourceIdentifier`;
- `sourceStandard`; and
- `sourceDataset`.

A source identifier retained as a Property shall not replace the NGSI-LD
Entity `id`.

### 8.2 Operational State

Typical properties include:

- `operationalStatus`;
- `scheduleRelationship`;
- `delay`;
- `currentStopSequence`;
- `speed`;
- `bearing`;
- `occupancyStatus`;
- `occupancyPercentage`; and
- `congestionLevel`.

Operational state properties may change without changing Entity identity.

### 8.3 Temporal Information

Typical properties include:

- `observedAt`;
- `reportedAt`;
- `retrievedAt`;
- `generatedAt`;
- `validFrom`;
- `validTo`;
- `startTime`; and
- `endTime`.

Different temporal meanings shall use distinct attributes.

A single generic timestamp shall not be used to collapse observation time,
source publication time, prediction time, and ingestion time.

### 8.4 Sequence and Pattern Information

Typical properties include:

- `sequence`;
- `direction`;
- `boardingAllowed`;
- `alightingAllowed`;
- `requestStop`; and
- `timingPoint`.

Sequence values are meaningful within a JourneyPattern context and are not
global identifiers.

---

## 9. Core Entity Mapping

### 9.1 ServiceJourney

A ServiceJourney shall normally be represented as an NGSI-LD Entity.

Typical representation includes:

- `id`;
- `type`;
- Relationship to PublicTransportRoute;
- Relationship to JourneyPattern;
- relationship or applicability information for DayType or ServiceCalendar;
- passenger-facing destination information; and
- source provenance.

A ServiceJourney shall not embed the complete Route, JourneyPattern, or
calendar object as an opaque Property when those concepts are independently
identified Entities.

---

### 9.2 DatedVehicleJourney

A DatedVehicleJourney shall normally be represented as an NGSI-LD Entity.

Typical representation includes:

- Relationship to ServiceJourney where applicable;
- Relationship to OperatingDay;
- status such as scheduled, added, replaced, or cancelled;
- optional Relationship to assigned PhysicalVehicle; and
- source provenance.

The assigned PhysicalVehicle is not part of the DatedVehicleJourney's
semantic identity.

---

### 9.3 MonitoredVehicleJourney

A MonitoredVehicleJourney shall normally be represented as an NGSI-LD Entity.

Typical representation includes:

- Relationship to DatedVehicleJourney;
- Relationship to PhysicalVehicle where known;
- current operational status;
- current delay;
- current or next PointInJourneyPattern;
- current or next PublicTransportStop;
- observation or report time;
- realtime-source provenance; and
- references to estimated or observed passing information when defined.

TripUpdate and VehiclePosition information concerning the same semantic
journey should update the same MonitoredVehicleJourney Entity.

They shall not automatically create separate Entities based on message type.

---

### 9.4 PhysicalVehicle

A PhysicalVehicle shall normally be represented as an NGSI-LD Entity.

Typical representation includes:

- label;
- fleet number;
- registration number;
- vehicle type;
- accessibility information;
- current occupancy;
- current speed;
- current bearing;
- current observed location; and
- observation time.

A planned or current journey assignment shall be represented through a
Relationship rather than changing PhysicalVehicle identity.

---

### 9.5 JourneyPattern

A JourneyPattern shall normally be represented as an NGSI-LD Entity.

Typical representation includes:

- Relationship to PublicTransportRoute;
- planned geometry;
- direction;
- operational characteristics; and
- Relationships to its PointInJourneyPattern instances where required by the
  implementation profile.

The ordered pattern shall not be inferred solely from array order in an
opaque Property when PointInJourneyPattern instances are independently
represented.

---

### 9.6 PointInJourneyPattern

A PointInJourneyPattern shall normally be represented as an NGSI-LD Entity
when independent reference is required.

Typical representation includes:

- Relationship to JourneyPattern;
- Relationship to PublicTransportStop where applicable;
- sequence;
- boarding and alighting conditions;
- timing-point classification; and
- source provenance.

The same PublicTransportStop may be referenced by multiple
PointInJourneyPattern Entities in one JourneyPattern.

---

### 9.7 PublicTransportStop and TransportStation

PublicTransportStop and TransportStation shall remain distinct Entity Types.

A PublicTransportStop may have a Relationship to its parent
TransportStation.

Location and other spatial information shall be represented through
GeoProperties where applicable.

---

## 10. Location Mapping

### 10.1 PhysicalVehicle Location

The observed geographic position of a physical vehicle shall normally be
represented as a GeoProperty of the PhysicalVehicle Entity.

The observation time shall be preserved.

The same observation may also contribute to the progress state of the
MonitoredVehicleJourney, but contradictory duplicated positions shall not be
created.

### 10.2 Stop and Station Location

The geographic location of a PublicTransportStop or TransportStation shall
normally be represented as a GeoProperty.

Where a station or stop has an area rather than only a representative point,
the geometry may be represented using the appropriate GeoJSON geometry.

### 10.3 Journey Geometry

The planned geometry of a JourneyPattern shall remain distinct from:

- the current location of a PhysicalVehicle;
- the geographic location of a stop; and
- the actual historical path followed by a monitored journey.

---

## 11. Temporal and Observation Metadata

NGSI-LD temporal metadata shall be used according to its defined meaning.

Typical distinctions include:

- the time at which a value was observed;
- the time at which source information was generated;
- the time at which the value became valid;
- the time at which the value was ingested or retrieved; and
- the time represented by the value itself.

For example, the predicted arrival time at a stop and the time at which that
prediction was generated are different temporal values.

They shall not be represented by one undifferentiated timestamp.

---

## 12. Provenance

A conforming representation should preserve sufficient provenance for
traceability.

Relevant provenance may include:

- source standard;
- source feed;
- source authority;
- source dataset;
- source record identifier;
- GTFS-Realtime FeedEntity identifier;
- source timestamp;
- retrieval timestamp;
- mapping profile version; and
- software component or process that produced the representation.

Provenance information shall remain distinct from semantic identity.

The precise representation of provenance will be defined by a later profile.

---

## 13. Update Behaviour

### 13.1 State Updates

Changes to operational state should update the existing Entity representing
the same semantic instance.

Examples include changes to:

- vehicle location;
- speed;
- bearing;
- occupancy;
- delay;
- journey status;
- current stop; and
- predicted time.

### 13.2 Entity Creation

A new Entity shall be created when a new semantic instance exists according
to the applicable semantic model's `Identity` section.

A new source message, source record, or retrieval cycle does not by itself
justify creating a new semantic Entity.

### 13.3 Entity Deletion

Removal of a source record or realtime feed entity shall not automatically
delete the semantic Entity.

A mapping profile shall distinguish:

- deletion from a source feed;
- expiration of current monitoring information;
- cancellation of a dated journey;
- loss of source visibility; and
- termination of the real-world or semantic instance.

### 13.4 Freshness and Expiration

Realtime state may be assigned expiration or freshness metadata.

The applicable profile shall define:

- freshness period;
- stale-state treatment;
- expiration policy;
- source precedence; and
- conflict resolution.

---

## 14. Normalized and Concise Representations

NGSI-LD supports multiple JSON-LD representations of the same information.

The choice between normalized, concise, and other supported representations
does not change the semantic mapping.

SCRA profiles may select one representation for examples or interchange
conventions, but conforming implementations shall preserve the same
underlying Entity, Property, Relationship, and vocabulary semantics.

---

## 15. Illustrative Example

The following example is informative and uses provisional compact terms.

```json
{
  "id": "urn:ngsi-ld:MonitoredVehicleJourney:operator-a:20260803:trip-123",
  "type": "MonitoredVehicleJourney",
  "datedVehicleJourney": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:DatedVehicleJourney:operator-a:20260803:trip-123"
  },
  "operatedByVehicle": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:PhysicalVehicle:operator-a:vehicle-42"
  },
  "currentStop": {
    "type": "Relationship",
    "object": "urn:ngsi-ld:PublicTransportStop:operator-a:stop-10"
  },
  "delay": {
    "type": "Property",
    "value": 120,
    "unitCode": "SEC"
  },
  "observedAt": "2026-08-03T06:20:00Z",
  "@context": [
    "https://uri.etsi.org/ngsi-ld/v1/ngsi-ld-core-context-v1.9.jsonld",
    "https://scra-schema.org/contexts/mobility.jsonld"
  ]
}
```

This example does not establish final SCRA vocabulary terms, Entity
identifier syntax, context paths, or mandatory attributes.

---

## 16. Deferred Items

The following items are deferred to later specifications:

- final SCRA vocabulary IRIs;
- final JSON-LD context files;
- mandatory and optional attributes for each Entity Type;
- cardinality constraints;
- datatype constraints;
- validation schemas;
- passing-time Entity Types;
- alert and disruption Entity Types;
- provenance profile;
- temporal-history profile;
- multilingual-value profile; and
- versioning and deprecation policy.

---

## 17. Conformance

An NGSI-LD mapping conforms to this specification when it:

- preserves the normative meaning and identity of SCRA semantic concepts;
- represents independently identified concepts as NGSI-LD Entities where
  required;
- represents semantic references as Relationships;
- distinguishes Properties from Relationships;
- uses URI identifiers for Entities;
- defines vocabulary terms through an applicable JSON-LD context;
- does not reproduce source-format nesting as the canonical semantic model;
- distinguishes semantic identity from source identifiers;
- distinguishes planned, dated, monitored, physical, and spatial concepts;
- preserves temporal meaning and provenance;
- updates existing Entities when only operational state changes;
- does not delete semantic Entities solely because source feed records
  disappear; and
- does not introduce unsupported semantic certainty.

---

## 18. References

- ETSI NGSI-LD Specification:
  https://cim.etsi.org/NGSI-LD/official/

- ETSI NGSI-LD Core Information Model:
  https://cim.etsi.org/NGSI-LD/official/clause-4.html

- ETSI NGSI-LD Introduction:
  https://cim.etsi.org/NGSI-LD/official/introduction.html

- JSON-LD 1.1:
  https://www.w3.org/TR/json-ld11/

- SCRA Semantic Modeling Principles:
  ../specifications/semantic-modeling-principles.md

- SCRA Semantic Models:
  ../models/index.md

- SCRA GTFS Static Mapping:
  gtfs-static.md

- SCRA GTFS-Realtime Mapping:
  gtfs-realtime.md

---

© SCRA Project

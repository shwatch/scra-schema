# PointInJourneyPattern

## Status

Normative

---

## Definition

A PointInJourneyPattern represents one ordered point within a
JourneyPattern.

It identifies the position of a point in the planned operational sequence of
a JourneyPattern.

A PointInJourneyPattern is distinct from the physical location itself.

Multiple PointInJourneyPattern instances may reference the same
PublicTransportStop when a JourneyPattern visits that stop more than once.

---

## Identity

The semantic identity of a PointInJourneyPattern is established by:

- the JourneyPattern to which it belongs; and
- its position within that JourneyPattern.

A PointInJourneyPattern remains the same semantic instance when:

- descriptive information changes;
- multiple ServiceJourneys use the same JourneyPattern; or
- different OperatingDays use the same JourneyPattern.

Changing the order of points normally establishes a different
PointInJourneyPattern.

Within a `JourneyPattern`, the occurrence discriminator shall identify the
ordered point independently of the referenced `PublicTransportStop`.

Where the same `PublicTransportStop` is visited more than once, each visit
shall have a distinct PointInJourneyPattern identity.

For GTFS `stop_times.txt`, `stop_sequence` shall be used as the primary
occurrence discriminator unless an applicable profile defines a more stable
equivalent. The GTFS `stop_id` identifies the referenced
`PublicTransportStop`; it shall not by itself identify the
PointInJourneyPattern occurrence.

A `PassingEvent` shall use the applicable PointInJourneyPattern identity,
sequence, or an equivalent profile-defined discriminator when required to
distinguish repeated visits to the same stop.

Implementation-specific identifier construction is defined by the relevant
mapping specification.

---

## Motivation

Public transport operations require an ordered representation of the planned
journey.

Physical stops alone cannot express:

- visit order;
- repeated visits to the same stop;
- timing points;
- operational control points; or
- non-stopping operational locations.

PointInJourneyPattern provides this ordered operational structure.

---

## Characteristics

A PointInJourneyPattern:

- belongs to exactly one JourneyPattern;
- occupies one position in the ordered sequence;
- may reference one PublicTransportStop;
- may represent a timing point;
- may represent a non-stopping operational point; and
- may be referenced by planned and monitored timing information.

---

## Relationships

### JourneyPattern

Each PointInJourneyPattern belongs to one JourneyPattern.

Its sequence within that JourneyPattern contributes to the planned
operational pattern.

---

### PublicTransportStop

A PointInJourneyPattern may reference one PublicTransportStop.

The same PublicTransportStop may be referenced by multiple
PointInJourneyPattern instances, including repeated visits within the same
JourneyPattern.

---

### PassingEvent

A PointInJourneyPattern may be referenced by zero or more
`PassingEvent` entities.

Each associated `PassingEvent` represents an operational event at the
ordered journey-pattern point.

Scheduled, predicted, and observed times are properties of the associated
`PassingEvent`, not of the PointInJourneyPattern itself.

---

## Typical Properties

Typical semantic properties include:

- identifier
- sequence
- stop
- timingPoint
- boardingAllowed
- alightingAllowed
- requestStop

PassingEvent timing values are not embedded as properties of the
PointInJourneyPattern.

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Conformance Requirements

A conforming `PointInJourneyPattern` shall include:

- a stable semantic identity;
- one relationship to the applicable `JourneyPattern`;
- one position or sequence discriminator within that `JourneyPattern`.

The following conditional requirements apply:

- a `PublicTransportStop` relationship shall be present when the ordered point
  represents a physical passenger stop;
- repeated visits to the same `PublicTransportStop` within one
  `JourneyPattern` shall be represented by distinct PointInJourneyPattern
  instances;
- boarding, alighting, timing-point, and request-stop properties shall be
  present only when those operational semantics are known or required by the
  applicable profile;
- a `PassingEvent` associated with the point shall reference the same
  operational occurrence within the applicable monitored journey.

The following information is optional unless required by an applicable
profile:

- a `PublicTransportStop` relationship;
- timing-point indication;
- boarding permission;
- alighting permission;
- request-stop indication;
- source-specific identifiers.

Scheduled, predicted, and observed event times shall be represented by
associated `PassingEvent` entities and shall not be embedded as temporal
properties of the `PointInJourneyPattern`.

---

## Origin

This semantic concept is adopted primarily from:

- Transmodel — `POINT IN JOURNEY PATTERN`

SCRA Schema adopts the Transmodel definition without changing its
fundamental meaning.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| Transmodel `POINT IN JOURNEY PATTERN` | PointInJourneyPattern |
| GTFS `stop_times.txt` stop sequence | Ordered PointInJourneyPattern |
| GTFS `stop_id` | Reference to PublicTransportStop |

Each GTFS `stop_times.txt` record normally contributes one
PointInJourneyPattern within the corresponding JourneyPattern.

The `stop_sequence` establishes the ordering and occurrence discriminator of
the PointInJourneyPattern instances. The `stop_id` resolves the referenced
PublicTransportStop.

These mappings shall preserve repeated-stop occurrences as distinct
PointInJourneyPattern instances.

The mapping table remains informative; the identity and occurrence rules in
the normative sections above are binding.

---

## See Also

- JourneyPattern
- PublicTransportStop
- ServiceJourney
- MonitoredVehicleJourney

---

© SCRA Project

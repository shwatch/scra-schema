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
PointInJourneyPattern instances.

---

### Passing Times

Scheduled, target, estimated, and observed passing times may all refer to a
PointInJourneyPattern.

The PointInJourneyPattern provides the common operational location to which
those timing values apply.

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

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

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

The `stop_sequence` establishes the ordering of the
PointInJourneyPattern instances.

This section is informative and does not form part of the normative semantic
definition.

---

## See Also

- JourneyPattern
- PublicTransportStop
- ServiceJourney
- MonitoredVehicleJourney

---

© SCRA Project

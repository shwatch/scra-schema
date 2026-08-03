# JourneyPattern

## Status

Normative

---

## Definition

A JourneyPattern defines the ordered sequence of points followed by a
ServiceJourney during its planned operation.

It specifies the operational pattern of a journey independently of any
particular operating day, vehicle assignment, or real-time operational
conditions.

A JourneyPattern represents the planned sequence of locations that a vehicle
journey follows and is independent of passenger-facing route identities.

---

## Identity

The semantic identity of a JourneyPattern is established by the planned
ordered sequence of PointInJourneyPattern instances that it contains.

A JourneyPattern remains the same semantic instance when:

- different ServiceJourneys follow the same pattern;
- different OperatingDays use the same pattern;
- different PhysicalVehicles operate journeys using the pattern; or
- real-time operational conditions differ.

Minor revisions to descriptive information do not necessarily establish a
new JourneyPattern.

Changes to the planned ordered sequence of points normally establish a
different JourneyPattern.

Implementation-specific identifier construction is defined by the relevant
mapping specification.

---

## Motivation

Public transport services frequently share common operational patterns.

Multiple ServiceJourneys may follow the same ordered sequence of stops while
operating at different times or on different days.

Separating the JourneyPattern from the ServiceJourney avoids duplication and
provides a stable semantic representation of the planned operational path.

---

## Characteristics

A JourneyPattern:

- represents a planned operational pattern;
- consists of an ordered sequence of PointInJourneyPattern instances;
- may be referenced by multiple ServiceJourneys;
- is independent of any specific OperatingDay;
- is independent of any PhysicalVehicle; and
- is independent of real-time operational information.

---

## Relationships

### ServiceJourney

A JourneyPattern may be followed by one or more ServiceJourneys.

Each ServiceJourney normally follows one JourneyPattern.

---

### PointInJourneyPattern

A JourneyPattern consists of one or more PointInJourneyPattern instances.

The ordering of these points forms the planned operational sequence.

---

### PublicTransportRoute

A JourneyPattern may be associated with one PublicTransportRoute.

A route groups related services for passengers, whereas the JourneyPattern
defines the detailed operational sequence.

---

## Typical Properties

Typical semantic properties include:

- identifier
- name
- direction
- sequence of PointInJourneyPattern
- geometry
- operational characteristics

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Origin

This semantic concept is adopted primarily from:

- Transmodel — `JOURNEY PATTERN`

SCRA Schema adopts the Transmodel definition without changing its
fundamental meaning.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| Transmodel `JOURNEY PATTERN` | JourneyPattern |
| GTFS `shapes.txt` | Geometry contributing to JourneyPattern |
| GTFS ordered `stop_times.txt` | Ordered PointInJourneyPattern sequence |

GTFS does not explicitly define JourneyPattern.

A JourneyPattern is inferred from the ordered operational structure of
`stop_times.txt`, optionally supported by `shapes.txt`.

This section is informative and does not form part of the normative semantic
definition.

---

## See Also

- ServiceJourney
- PointInJourneyPattern
- PublicTransportRoute

---

© SCRA Project

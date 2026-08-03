# ServiceJourney

## Status

Normative

---

## Definition

A ServiceJourney represents a scheduled passenger-carrying public transport
journey defined within a long-term or recurring service plan.

It describes the planned service independently of any specific operating day,
PhysicalVehicle assignment, or real-time operational state.

A ServiceJourney represents the service pattern that passengers are intended
to use according to the published or planned timetable.

---

## Identity

The semantic identity of a ServiceJourney is established by the identity of
the scheduled journey within the recurring service plan.

A ServiceJourney remains the same semantic instance when:

- it operates on a different operating day;
- a different PhysicalVehicle is assigned;
- delays or other real-time conditions occur;
- predicted or observed passing times change; or
- its operational status changes for a particular day.

A change to the underlying planned journey that creates a distinct scheduled
service, such as the introduction of a separate journey in the timetable,
results in a different ServiceJourney.

Minor timetable revisions do not automatically determine whether a new
ServiceJourney exists. The relevant planning authority or source
specification shall define whether the revised journey preserves or replaces
the prior scheduled identity.

Implementation-specific identifier construction is defined by the relevant
mapping specification.

---

## Motivation

Public transport services are commonly planned as recurring journeys that may
operate on many different days.

The same planned journey may:

- operate repeatedly according to a service calendar;
- be assigned different PhysicalVehicles;
- experience different operational conditions; and
- produce different real-time observations on each operating day.

These operational variations do not change the identity of the recurring
scheduled service.

ServiceJourney therefore separates the long-term scheduled service definition
from its dated occurrence and monitored execution.

---

## Characteristics

A ServiceJourney:

- represents a scheduled passenger-carrying journey;
- belongs to a recurring or long-term service plan;
- normally follows one journey pattern;
- may operate on multiple operating days;
- may give rise to multiple DatedVehicleJourneys;
- is independent of any specific PhysicalVehicle;
- is independent of real-time operational conditions; and
- is distinct from a route, journey pattern, and dated journey occurrence.

---

## Relationships

### PublicTransportRoute

A ServiceJourney may be associated with one PublicTransportRoute.

The route provides the passenger-facing or operational grouping within which
the journey is offered.

The route is not the detailed ordered path of the ServiceJourney.

---

### Journey Pattern

A ServiceJourney normally follows one journey pattern.

The journey pattern defines the ordered sequence of stopping points and the
planned path followed by the journey.

A journey pattern is distinct from the broader PublicTransportRoute.

---

### PublicTransportStop

A ServiceJourney serves PublicTransportStops through its journey pattern and
associated stopping pattern.

The ordered stop sequence shall not be inferred solely from the route
identity.

---

### DatedVehicleJourney

A ServiceJourney may give rise to one or more DatedVehicleJourneys.

Each DatedVehicleJourney represents a planned occurrence of the journey on a
specific operating day.

A DatedVehicleJourney may also be introduced specifically for an operating
day without corresponding to an existing ServiceJourney.

---

### Operator

A ServiceJourney may be operated by an organisation.

The planned operator and the organisation performing the actual service on a
particular day may differ where operational substitution occurs.

---

## Typical Properties

Typical semantic properties include:

- identifier
- name
- route
- journeyPattern
- operator
- direction
- serviceType
- scheduledPassingTimes
- accessibilityFeatures
- destinationDisplay

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Origin

This semantic concept is adopted primarily from:

- Transmodel — `SERVICE JOURNEY`
- Transmodel — `VEHICLE JOURNEY`
- Transmodel — `JOURNEY PATTERN`

Transmodel defines a `SERVICE JOURNEY` as a passenger-carrying vehicle journey
for which passengers may be allowed to board or alight.

SCRA Schema preserves the distinction between:

- the recurring scheduled ServiceJourney;
- its journey pattern;
- its dated occurrence; and
- its monitored operational execution.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| GTFS Static `trips.txt` record | ServiceJourney |
| GTFS Static `trip_id` | Source identifier for ServiceJourney |
| GTFS Static `route_id` | Reference to PublicTransportRoute |
| GTFS Static `direction_id` | Direction classification |
| GTFS Static `shape_id` | Possible source for planned path geometry |
| GTFS Static stop-time sequence | Scheduled stop sequence and passing times |

A GTFS `trip_id` identifies a trip within the scope of one GTFS dataset and
shall not automatically be treated as a globally unique semantic identifier.

The mapping specification shall define source authority, identifier scope,
versioning, and treatment of timetable revisions.

This section is informative and does not form part of the normative semantic
definition.

---

## See Also

- PublicTransportRoute
- PublicTransportStop
- DatedVehicleJourney
- MonitoredVehicleJourney
- ADR-0003

---

© SCRA Project

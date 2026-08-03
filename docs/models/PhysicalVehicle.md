# PhysicalVehicle

## Status

Normative

---

## Definition

A PhysicalVehicle represents an individual physical means of transport used
to provide transport services.

It represents the transport asset itself, independently of the journeys,
routes, operating days, or services to which it is assigned.

A PhysicalVehicle may be assigned to different transport operations during
its operational lifetime.

---

## Identity

The semantic identity of a PhysicalVehicle is established by the identity of
the physical transport asset itself.

A PhysicalVehicle remains the same semantic instance when:

- it is assigned to a different journey;
- it operates on a different route;
- it is assigned to a different operating day;
- its operational state changes;
- its public-facing label or fleet number changes; or
- its recorded properties are updated.

A change of identifier in a source system does not by itself establish that
a different PhysicalVehicle exists.

A replacement vehicle, even when assigned to the same journey or operational
duty, represents a distinct PhysicalVehicle.

The construction of implementation identifiers for PhysicalVehicle instances
is defined by the relevant mapping specification and is not prescribed by
this semantic model.

---

## Motivation

Transport information systems frequently use the term `vehicle` for
different concepts, including:

- a physical transport asset;
- a type or classification of vehicle;
- a scheduled vehicle operation;
- a real-time vehicle observation; and
- a vehicle assigned to a particular journey.

These concepts shall remain semantically distinct.

PhysicalVehicle represents only the individual physical transport asset.

Separating the vehicle from the journey allows the same vehicle to be
assigned to multiple operations without changing its semantic identity.

---

## Characteristics

A PhysicalVehicle:

- represents one individual physical transport asset;
- may be classified by one or more vehicle characteristics;
- may be assigned to different journeys over time;
- may carry equipment and accessibility facilities;
- exists independently of any particular route or timetable;
- exists independently of real-time observations; and
- is distinct from a vehicle type or vehicle model.

---

## Relationships

### Vehicle Type

A PhysicalVehicle may be classified by a vehicle type.

The vehicle type describes common characteristics shared by multiple
vehicles, such as transport mode, capacity, configuration, or operational
capabilities.

The vehicle type is not the physical vehicle itself.

---

### DatedVehicleJourney

A PhysicalVehicle may be assigned to operate one or more
DatedVehicleJourneys over time.

A DatedVehicleJourney may exist before a PhysicalVehicle has been assigned
to it.

Vehicle assignment does not determine the semantic identity of either the
PhysicalVehicle or the DatedVehicleJourney.

---

### MonitoredVehicleJourney

A PhysicalVehicle may operate a MonitoredVehicleJourney.

A MonitoredVehicleJourney describes the monitored operational execution of a
journey and does not replace the identity of the PhysicalVehicle.

---

### Operator

A PhysicalVehicle may be owned, managed, or operated by an organisation.

Ownership, management, and operational responsibility are separate semantic
relationships and shall not be treated as equivalent unless a source
specification explicitly establishes that equivalence.

---

## Typical Properties

Typical semantic properties include:

- identifier
- label
- fleetNumber
- registrationNumber
- vehicleType
- transportMode
- operator
- capacity
- accessibilityFeatures
- onboardEquipment
- operationalStatus

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Origin

This semantic concept is harmonized primarily from:

- Transmodel — `VEHICLE`
- Transmodel — `VEHICLE TYPE`
- GTFS-Realtime — `VehicleDescriptor`
- Schema.org — `Vehicle`

Transmodel defines `VEHICLE` as a physical public transport vehicle used for
short-term operational planning and daily assignment.

SCRA Schema uses the name `PhysicalVehicle` to make the physical nature of
the concept explicit and to distinguish it from vehicle journeys, vehicle
types, and source-specific uses of the term `Vehicle`.

This naming clarification does not change the fundamental meaning of the
Transmodel concept.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| Transmodel `VEHICLE` | PhysicalVehicle |
| GTFS-Realtime `VehicleDescriptor.id` | Source identifier for PhysicalVehicle |
| GTFS-Realtime `VehicleDescriptor.label` | PhysicalVehicle label |
| GTFS-Realtime `VehicleDescriptor.license_plate` | PhysicalVehicle registration number |

A source identifier shall not automatically be assumed to provide globally
stable semantic identity.

Where a data source does not provide a persistent identifier for the
physical asset, the mapping shall document the scope and limitations of the
resulting identity.

This section is informative and does not form part of the normative semantic
definition.

---

## See Also

- DatedVehicleJourney
- MonitoredVehicleJourney
- ADR-0005

---

© SCRA Project

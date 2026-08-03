# DatedVehicleJourney

## Status

Normative

---

## Definition

A DatedVehicleJourney represents a vehicle journey planned for one specific
operating day.

It provides the dated operational plan for a transport journey.

A DatedVehicleJourney may be derived from a regularly scheduled
ServiceJourney or may be introduced specifically for the operating day.

It exists independently of its real-time execution and independently of the
PhysicalVehicle assigned to operate it.

---

## Identity

The semantic identity of a DatedVehicleJourney is established by the
identity of the planned journey occurrence within a specific operating day.

For a regularly scheduled journey, identity normally depends on:

- the underlying scheduled journey; and
- the operating day on which it is planned to operate.

A DatedVehicleJourney may also represent an additional or specially created
journey that has no equivalent regularly scheduled journey.

In that case, its identity shall be established within the dated operational
plan for the relevant operating day.

A DatedVehicleJourney remains the same semantic instance when:

- its delay changes;
- its predicted passing times change;
- a PhysicalVehicle is assigned or replaced;
- its monitored operational state changes; or
- operational updates modify its current execution without replacing the
  underlying planned journey occurrence.

A newly introduced journey, or a replacement that constitutes a distinct
planned journey occurrence, represents a different DatedVehicleJourney.

Cancellation does not by itself erase the semantic identity of the
DatedVehicleJourney. It changes the status of the planned journey
occurrence.

Implementation-specific identifier construction is defined by the relevant
mapping specification.

---

## Motivation

A scheduled transport service may normally be associated with a recurring
day type or service calendar.

Operational planning requires a representation of what is planned for one
specific operating day.

Short-term circumstances may cause journeys to be:

- added;
- cancelled;
- shortened;
- extended;
- diverted; or
- otherwise modified for a particular day.

Changing the recurring timetable definition for every short-term
modification would incorrectly conflate long-term service planning with the
dated operational plan.

DatedVehicleJourney therefore represents the planned journey occurrence for
one operating day.

---

## Characteristics

A DatedVehicleJourney:

- is planned for exactly one operating day;
- may be based on a ServiceJourney;
- may be created specifically for the operating day;
- may contain dated passing-time information;
- may be assigned a PhysicalVehicle;
- may be monitored during operation;
- may be cancelled or otherwise altered;
- remains distinct from its real-time monitored state; and
- remains distinct from the physical vehicle assigned to operate it.

---

## Relationships

### Operating Day

Each DatedVehicleJourney belongs to exactly one operating day.

The operating day provides the temporal context of the planned journey
occurrence.

An operating day may cross the civil-calendar midnight boundary according to
the operational conventions of the transport provider.

---

### ServiceJourney

A DatedVehicleJourney may be based on one ServiceJourney.

The ServiceJourney provides the recurring scheduled service definition from
which a normal dated journey may be derived.

A DatedVehicleJourney created specifically for an operating day is not
required to correspond to an existing ServiceJourney.

---

### PhysicalVehicle

A DatedVehicleJourney may be assigned a PhysicalVehicle.

The assignment may be unknown, provisional, or changed during operation.

A vehicle assignment does not determine the identity of the
DatedVehicleJourney.

---

### MonitoredVehicleJourney

A DatedVehicleJourney may be monitored as a MonitoredVehicleJourney.

The MonitoredVehicleJourney represents the observed or estimated operational
execution of the dated plan.

The DatedVehicleJourney and MonitoredVehicleJourney are distinct semantic
concepts.

---

### Dated Passing Time

A DatedVehicleJourney may have one or more dated passing times.

A dated passing time describes a planned arrival, departure, or passing event
within the context of the specific operating day.

---

## Typical Properties

Typical semantic properties include:

- identifier
- operatingDay
- serviceJourney
- journeyPattern
- plannedPassingTimes
- serviceAlteration
- cancellationStatus
- assignedVehicle
- operator

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Origin

This semantic concept is adopted primarily from:

- Transmodel — `DATED VEHICLE JOURNEY`
- Transmodel — `NORMAL DATED VEHICLE JOURNEY`
- Transmodel — `EXTRA DATED VEHICLE JOURNEY`

Transmodel defines a `DATED VEHICLE JOURNEY` as a vehicle journey planned
for one specific operating day.

A normal dated journey may be based on a longer-term planned vehicle journey,
while an extra dated journey may be introduced specifically into the dated
operational plan.

SCRA Schema preserves this distinction and does not restrict every
DatedVehicleJourney to an existing recurring ServiceJourney.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| GTFS Static `trip_id` | Reference to the underlying scheduled journey |
| GTFS Static service date | Operating day |
| GTFS-Realtime `TripDescriptor` | Identification and state of a dated journey |
| GTFS-Realtime `TripDescriptor.start_date` | Operating-day context |
| GTFS-Realtime added trip | Additional DatedVehicleJourney |
| GTFS-Realtime canceled trip | Cancellation state of DatedVehicleJourney |

A GTFS-Realtime `TripDescriptor` does not always provide all information
required to establish a globally unique semantic identity.

The mapping specification shall define identifier scope, source authority,
operating-day interpretation, and handling of added or replacement journeys.

This section is informative and does not form part of the normative semantic
definition.

---

## See Also

- ServiceJourney
- PhysicalVehicle
- MonitoredVehicleJourney
- ADR-0004

---

© SCRA Project

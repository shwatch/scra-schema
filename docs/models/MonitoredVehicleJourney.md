# MonitoredVehicleJourney

## Status

Normative

---

## Definition

A MonitoredVehicleJourney represents the current operational state of a
DatedVehicleJourney that is being monitored during execution.

It provides a canonical semantic representation of an ongoing public
transport journey, including its observed operational status, current
progress, and predicted future operation.

A MonitoredVehicleJourney is independent of any particular real-time data
format or monitoring technology.

---

## Identity

The semantic identity of a MonitoredVehicleJourney is established by the
identity of the monitored operational journey.

It represents the real-time execution of one DatedVehicleJourney.

Its identity remains stable throughout the execution of that journey,
regardless of updates received from monitoring systems.

For a regularly scheduled service, identity normally depends on:

- DatedVehicleJourney
- Operating Day

Where more than one monitored execution may correspond to the same
DatedVehicleJourney on the same Operating Day, identity shall additionally
include a start time, execution identifier, or another profile-defined
discriminator.

The assigned PhysicalVehicle is not part of the semantic identity.

Real-time observations update the state of the MonitoredVehicleJourney but
do not change its identity.

---

## Motivation

Transport operators continuously publish operational information describing
the current state of vehicle journeys.

Different technologies represent this information differently.

SCRA defines a canonical semantic model that captures the operational meaning
independently of any implementation technology.

This enables consistent semantic interoperability across multiple monitoring
systems.

---

## Characteristics

A MonitoredVehicleJourney:

- represents one journey currently being monitored;
- exists only while operational monitoring is available;
- evolves continuously as new observations become available;
- remains independent of the PhysicalVehicle performing the journey;
- remains independent of implementation technologies.

---

## Relationships

A MonitoredVehicleJourney:

- monitors one DatedVehicleJourney;
- may currently be operated by one PhysicalVehicle;
- may be referenced by zero or more `PassingEvent` entities representing
  scheduled, predicted, or observed operational events;
- may be associated with operational observations;
- may reference one or more monitoring sources.

---

## Typical Properties

Typical properties include:

- current operational status;
- current geographical position;
- current delay;
- current bearing;
- current speed;
- occupancy information;
- observation timestamp;
- prediction timestamp;
- current stop;
- next stop.

Predicted, scheduled, and observed stop events are represented by associated
`PassingEvent` entities rather than embedded predicted passing-time structures.

These properties are informative examples rather than mandatory attributes.

---

## Conformance Requirements

A conforming `MonitoredVehicleJourney` shall include:

- a stable semantic identity for the monitored execution;
- one relationship to the applicable `DatedVehicleJourney`;
- the applicable Operating Day or another profile-defined service-date
  discriminator.

The following conditional requirements apply:

- a `PhysicalVehicle` relationship shall be present only when a vehicle is
  currently assigned and known;
- a current geographical position shall include its applicable observation
  time;
- a current delay shall conform to the normative `delay` calculation rules and
  shall have an applicable baseline time;
- predicted, scheduled, or observed stop events shall be represented by
  associated `PassingEvent` entities;
- prediction confidence shall be used only for predicted operational values;
- occupancy information shall identify its applicable observation time or
  source context when required by the applicable profile.

The following information is optional unless required by an applicable
profile:

- the currently assigned `PhysicalVehicle`;
- current geographical position;
- current bearing;
- current speed;
- current delay;
- occupancy information;
- current stop;
- next stop;
- observation timestamp;
- prediction timestamp;
- references to monitoring sources.

A `MonitoredVehicleJourney` may be referenced by zero or more `PassingEvent`
entities. Each associated `PassingEvent` shall reference exactly one
`MonitoredVehicleJourney`.

Updates to an associated `PassingEvent` shall not alter the identity of the
`MonitoredVehicleJourney`.

---

## Origin

Typical sources include:

- GTFS-Realtime VehiclePosition
- GTFS-Realtime TripUpdate
- SIRI Vehicle Monitoring
- SIRI Estimated Timetable
- Automatic Vehicle Location (AVL)
- Operational control systems

The semantic model is independent of any particular source.

---

## Mapping Guidance

Typical mappings include:

- GTFS-Realtime → MonitoredVehicleJourney
- SIRI → MonitoredVehicleJourney
- AVL → MonitoredVehicleJourney
- MonitoredVehicleJourney → NGSI-LD
- MonitoredVehicleJourney → JSON-LD
- MonitoredVehicleJourney → RDF
- MonitoredVehicleJourney → MCP Resources

Mappings are informative and shall not modify the semantic definition.

---

## See Also

- ServiceJourney
- DatedVehicleJourney
- PhysicalVehicle
- PassingTime

---

© SCRA Project

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
- produces PassingTime predictions;
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
- observed timestamp;
- prediction timestamp;
- current stop;
- next stop;
- predicted passing times.

These properties are informative examples rather than mandatory attributes.

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

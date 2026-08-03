# PassingEvent

## Status

Normative

---

## Definition

A PassingEvent represents the operational event of a vehicle journey passing,
arriving at, or departing from a PublicTransportStop.

It is an operational event associated with the execution of a
MonitoredVehicleJourney.

A PassingEvent represents the occurrence itself.

Scheduled, predicted, and observed times describe different temporal aspects
of the same event rather than different semantic concepts.

---

## Identity

The semantic identity of a PassingEvent is established by:

- the associated MonitoredVehicleJourney;
- the PublicTransportStop;
- the operational event type.

Changes to scheduled, predicted, or observed times do not alter the identity
of the PassingEvent.

They update the temporal properties of the same event.

---

## Motivation

Public transport systems publish multiple time values describing the same
operational event.

Examples include:

- scheduled arrival;
- predicted arrival;
- observed arrival;
- scheduled departure;
- predicted departure;
- observed departure.

Although these values may differ, they all refer to the same operational
event.

SCRA therefore models the event itself rather than individual timestamps.

---

## Characteristics

A PassingEvent:

- belongs to one MonitoredVehicleJourney;
- refers to one PublicTransportStop;
- represents one operational event;
- may contain scheduled, predicted, and observed times;
- may be updated repeatedly as operational information changes.

---

## Relationships

### MonitoredVehicleJourney

Each PassingEvent belongs to one MonitoredVehicleJourney.

The monitored journey provides the operational context of the event.

---

### PublicTransportStop

Each PassingEvent refers to one PublicTransportStop.

The stop defines where the operational event occurs.

---

## Typical Properties

Typical semantic properties include:

- eventType
- scheduledTime
- predictedTime
- observedTime
- predictionConfidence
- observationTimestamp
- source

The semantic model intentionally does not prescribe implementation-specific
property names or data structures.

---

## Origin

This semantic concept harmonizes several existing concepts, including:

- Transmodel — Passing Time
- GTFS Stop Time
- GTFS-Realtime StopTimeUpdate
- SIRI Estimated Call
- SIRI Monitored Call

SCRA models the operational event itself as the canonical semantic concept.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| GTFS StopTime | PassingEvent |
| GTFS-Realtime StopTimeUpdate | PassingEvent |
| SIRI EstimatedCall | PassingEvent |
| SIRI MonitoredCall | PassingEvent |

Mappings are informative and do not modify the semantic definition.

---

## See Also

- MonitoredVehicleJourney
- DatedVehicleJourney
- PublicTransportStop

---

© SCRA Project

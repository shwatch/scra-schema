# PassingEvent

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/PassingEvent
```

## Kind

NGSI-LD Entity Type

## Definition

A PassingEvent represents the operational event of a vehicle journey passing,
arriving at, or departing from a PublicTransportStop.

Scheduled, predicted, and observed times describe temporal aspects of the same
event. Updates to those values do not create a new PassingEvent while the
journey, stop, and operational event identity remain unchanged.

## Typical Relationships

- `monitoredVehicleJourney`
- `stop`

## Typical Properties

- `eventType`
- `scheduledTime`
- `predictedTime`
- `observedTime`
- `predictionConfidence`
- `observationTimestamp`
- `delay`
- `minutesUntilArrival`
- `referenceTime`

## Source Alignment

- Transmodel Passing Time
- GTFS Stop Time
- GTFS-Realtime StopTimeUpdate
- SIRI EstimatedCall
- SIRI MonitoredCall

## See Also

- [PassingEvent semantic model](../models/PassingEvent.md)
- [ADR-0006](../decisions/0006-realtime-passing-times.md)

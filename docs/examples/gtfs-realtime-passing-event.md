# GTFS Realtime to SCRA Examples

## Status

Draft

## Purpose

This document provides worked examples of the normative mapping from GTFS
Schedule and GTFS Realtime data to SCRA `MonitoredVehicleJourney`,
`PointInJourneyPattern`, and `PassingEvent` entities.

The GTFS Realtime source fragments are shown as illustrative JSON equivalents
of the protobuf messages. They are not a replacement for the official protobuf
schema.

---

## Common Static Data

The examples use the following simplified GTFS Schedule records.

### `trips.txt`

```csv
route_id,service_id,trip_id
R1,WKD,T100
```

### `stop_times.txt`

```csv
trip_id,arrival_time,departure_time,stop_id,stop_sequence
T100,08:00:00,08:00:30,S1,1
T100,08:10:00,08:10:30,S2,2
T100,08:20:00,08:20:30,S1,3
```

The same `stop_id` (`S1`) occurs twice. Therefore, `stop_id` alone does not
identify the journey occurrence. The two visits are distinguished by
`stop_sequence` values `1` and `3`.

The examples use the service date `2026-08-06` and the source namespace
`toei-bus`.

---

## Example 1: Predicted Arrival at One Occurrence

### GTFS Realtime source

```json
{
  "id": "tu-T100-20260806",
  "trip_update": {
    "trip": {
      "trip_id": "T100",
      "start_date": "20260806",
      "schedule_relationship": "SCHEDULED"
    },
    "timestamp": 1785970500,
    "stop_time_update": [
      {
        "stop_sequence": 2,
        "stop_id": "S2",
        "arrival": {
          "time": 1785971520,
          "delay": 120
        }
      }
    ]
  }
}
```

The StopTimeUpdate resolves to the second ordered occurrence in the trip.
The static scheduled arrival is `2026-08-06T08:10:00+09:00`; the predicted
arrival is `2026-08-06T08:12:00+09:00`.

### SCRA `MonitoredVehicleJourney`

```json
{
  "id": "urn:mobility:monitored-vehicle-journey:toei-bus:T100:2026-08-06",
  "type": "MonitoredVehicleJourney",
  "datedVehicleJourney": {
    "type": "Relationship",
    "object": "urn:mobility:dated-vehicle-journey:toei-bus:T100:2026-08-06"
  },
  "operatingDay": {
    "type": "Property",
    "value": "2026-08-06"
  },
  "@context": "https://scra-schema.org/contexts/mobility.jsonld"
}
```

### SCRA arrival `PassingEvent`

```json
{
  "id": "urn:mobility:passing-event:toei-bus:T100:2026-08-06:2:arrival",
  "type": "PassingEvent",
  "monitoredVehicleJourney": {
    "type": "Relationship",
    "object": "urn:mobility:monitored-vehicle-journey:toei-bus:T100:2026-08-06"
  },
  "stop": {
    "type": "Relationship",
    "object": "urn:mobility:public-transport-stop:toei-bus:S2"
  },
  "eventType": {
    "type": "Property",
    "value": "arrival"
  },
  "scheduledTime": {
    "type": "Property",
    "value": "2026-08-06T08:10:00+09:00"
  },
  "predictedTime": {
    "type": "Property",
    "value": "2026-08-06T08:12:00+09:00"
  },
  "delay": {
    "type": "Property",
    "value": 120,
    "unitCode": "SEC"
  },
  "observationTimestamp": {
    "type": "Property",
    "value": "2026-08-06T07:55:00+09:00"
  },
  "@context": "https://scra-schema.org/contexts/mobility.jsonld"
}
```

### Mapping notes

- `stop_sequence = 2` selects the `PointInJourneyPattern` occurrence.
- `stop_id = S2` resolves the referenced `PublicTransportStop`.
- `arrival.time` maps to `predictedTime` because the source represents a
  forecast.
- `arrival.delay = 120` maps to signed whole-second `delay`.
- `TripUpdate.timestamp` is used as `observationTimestamp` only because this
  example profile defines it as the report time supporting the prediction.
- No `observedTime` is produced.

---

## Example 2: Arrival and Departure Are Distinct Events

### GTFS Realtime source

```json
{
  "id": "tu-T100-20260806-stop2",
  "trip_update": {
    "trip": {
      "trip_id": "T100",
      "start_date": "20260806"
    },
    "stop_time_update": [
      {
        "stop_sequence": 2,
        "stop_id": "S2",
        "arrival": {
          "time": 1785971520,
          "delay": 120
        },
        "departure": {
          "time": 1785971580,
          "delay": 90
        }
      }
    ]
  }
}
```

A single StopTimeUpdate contains two operational event types. It therefore
maps to two `PassingEvent` identities.

### Arrival event identity

```text
urn:mobility:passing-event:toei-bus:T100:2026-08-06:2:arrival
```

### Departure event identity

```text
urn:mobility:passing-event:toei-bus:T100:2026-08-06:2:departure
```

### SCRA departure `PassingEvent`

```json
{
  "id": "urn:mobility:passing-event:toei-bus:T100:2026-08-06:2:departure",
  "type": "PassingEvent",
  "monitoredVehicleJourney": {
    "type": "Relationship",
    "object": "urn:mobility:monitored-vehicle-journey:toei-bus:T100:2026-08-06"
  },
  "stop": {
    "type": "Relationship",
    "object": "urn:mobility:public-transport-stop:toei-bus:S2"
  },
  "eventType": {
    "type": "Property",
    "value": "departure"
  },
  "scheduledTime": {
    "type": "Property",
    "value": "2026-08-06T08:10:30+09:00"
  },
  "predictedTime": {
    "type": "Property",
    "value": "2026-08-06T08:13:00+09:00"
  },
  "delay": {
    "type": "Property",
    "value": 90,
    "unitCode": "SEC"
  },
  "@context": "https://scra-schema.org/contexts/mobility.jsonld"
}
```

Equal arrival and departure timestamps would not merge their semantic
identities. `eventType` remains part of `PassingEvent` identity.

---

## Example 3: Repeated Stop Occurrence

The static trip visits `S1` at both `stop_sequence = 1` and
`stop_sequence = 3`.

### Unambiguous update

```json
{
  "stop_sequence": 3,
  "stop_id": "S1",
  "arrival": {
    "delay": 180
  }
}
```

This update maps to the third `PointInJourneyPattern` occurrence and to the
following event identity:

```text
urn:mobility:passing-event:toei-bus:T100:2026-08-06:3:arrival
```

It shall not update the event at `stop_sequence = 1`.

### Ambiguous update

```json
{
  "stop_id": "S1",
  "arrival": {
    "delay": 180
  }
}
```

This update cannot be assigned safely because `S1` occurs twice in the
applicable trip. A conforming mapper shall report or reject the ambiguity. It
shall not silently choose the first or last occurrence.

---

## Example 4: Derived `minutesUntilArrival`

Assume the mapped arrival event contains:

```json
{
  "scheduledTime": "2026-08-06T08:10:00+09:00",
  "predictedTime": "2026-08-06T08:12:00+09:00"
}
```

and the calculation is performed at:

```text
referenceTime = 2026-08-06T08:08:31+09:00
```

The selected arrival time is `predictedTime`. The positive duration is
3 minutes and 29 seconds. After truncation toward zero:

```text
minutesUntilArrival = 3
```

The passenger-facing properties are:

```json
{
  "referenceTime": {
    "type": "Property",
    "value": "2026-08-06T08:08:31+09:00"
  },
  "minutesUntilArrival": {
    "type": "Property",
    "value": 3
  }
}
```

If the selected arrival time is earlier than `referenceTime`,
`minutesUntilArrival` is reported as `0`.

---

## Example 5: No Automatic `observedTime`

Consider a StopTimeUpdate retained in a feed after the vehicle has passed the
stop:

```json
{
  "stop_sequence": 2,
  "arrival": {
    "time": 1785971520
  }
}
```

The fact that the timestamp is earlier than mapper processing time does not
establish that the event was observed. Unless the source profile provides
explicit semantics showing that the event actually occurred, the value remains
a prediction or is left unmapped according to the profile.

A conforming mapper shall not convert it to `observedTime` based on temporal
position alone.

---

## Example 6: VehiclePosition and Observation Time

### GTFS Realtime source

```json
{
  "id": "vp-V100",
  "vehicle": {
    "trip": {
      "trip_id": "T100",
      "start_date": "20260806"
    },
    "vehicle": {
      "id": "V100"
    },
    "position": {
      "latitude": 35.6812,
      "longitude": 139.7671,
      "bearing": 180.0,
      "speed": 8.5
    },
    "current_stop_sequence": 2,
    "stop_id": "S2",
    "timestamp": 1785970500
  }
}
```

### Mapping result

- The TripDescriptor resolves the same `MonitoredVehicleJourney` used by the
  PassingEvents.
- `current_stop_sequence = 2` resolves the current ordered occurrence.
- `stop_id = S2` resolves the physical stop reference.
- `timestamp` is the observation time for the position reading.
- It is not a passing-event time and is not automatically the same as the
  FeedHeader timestamp.

---

## Conformance Checklist

An implementation of these examples shall verify that:

- source namespaces participate in mapped identities;
- service date participates in monitored-execution identity;
- `stop_sequence` distinguishes repeated occurrences;
- `stop_id` resolves a stop but does not replace the occurrence discriminator;
- arrival and departure create distinct `PassingEvent` identities;
- forecasts map to `predictedTime`;
- `observedTime` requires explicit source evidence;
- delay remains signed whole seconds;
- `observationTimestamp`, `referenceTime`, feed time, and event time remain
  distinct;
- derived `minutesUntilArrival` follows the normative calculation rules;
- all compact terms expand through the SCRA mobility context.

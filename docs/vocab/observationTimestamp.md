# observationTimestamp

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/observationTimestamp
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Value

ISO 8601 date-time

## Definition

The time at which the operational information supporting the PassingEvent
was observed or reported by the source system.

`observationTimestamp` shall represent the timestamp of the supporting
observation or report and shall not be interpreted as the time at which the
PassingEvent itself occurred.

`observationTimestamp` shall be expressed as an ISO 8601 date-time that
includes either a UTC offset or `Z`.

It is distinct from `scheduledTime`, `predictedTime`, `observedTime`,
`referenceTime`, feed retrieval time, publication time, and processing time.

# observedTime

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/observedTime
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Value

ISO 8601 date-time

## Definition

The actual time at which the PassingEvent occurred.

`observedTime` shall represent the actual occurrence time of the event and
shall not be interpreted as the time at which the observation was recorded,
received, published, or processed.

`observedTime` shall be expressed as an ISO 8601 date-time that includes
either a UTC offset or `Z`.

A value shall be classified as observed only when source semantics provide
sufficient evidence that the event occurred. Temporal position alone is not
sufficient evidence.

The corresponding observation or publication time, when required, shall be
represented by a separate property such as `observationTimestamp`.

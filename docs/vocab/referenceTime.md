# referenceTime

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/referenceTime
```

## Kind

NGSI-LD Property

## Domain

A derived or query-relative semantic result, including `PassingEvent`

## Value

ISO 8601 date-time

## Definition

The time used as the calculation basis for a derived temporal value such as
`minutesUntilArrival`.

A derived value that depends on time shall be interpreted together with the
`referenceTime` used to calculate it.

`referenceTime` and any event time used in the calculation shall be expressed
on a common time axis using an ISO 8601 date-time that includes either a UTC
offset or `Z`.

It is distinct from source observation time, prediction-generation time,
retrieval time, publication time, and the event time itself.

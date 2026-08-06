# scheduledTime

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/scheduledTime
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Value

ISO 8601 date-time

## Definition

The timetable or published schedule time associated with a PassingEvent.

`scheduledTime` shall be expressed as an ISO 8601 date-time that includes
either a UTC offset or `Z`.

It is distinct from predicted, target, and observed operational times.

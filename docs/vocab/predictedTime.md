# predictedTime

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/predictedTime
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Value

ISO 8601 date-time

## Definition

The forecast time at which the PassingEvent is expected to occur, calculated
from the latest available operational information.

`predictedTime` shall be expressed as an ISO 8601 date-time that includes
either a UTC offset or `Z`.

The time at which the prediction was produced shall be represented separately.

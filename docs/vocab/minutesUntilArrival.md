# minutesUntilArrival

## Status

Draft

## IRI

```text
https://scra-schema.org/vocab/minutesUntilArrival
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Value

Non-negative integer minutes

## Definition

A derived passenger-facing value representing the number of whole minutes
from `referenceTime` until the applicable predicted, observed, or scheduled
arrival time.

This value is reference-time dependent and shall not replace the underlying
absolute event time.

The calculation and rounding policy shall be documented by the producing
profile.

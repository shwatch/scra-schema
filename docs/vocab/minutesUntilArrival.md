# minutesUntilArrival

## Status

Normative

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

A derived passenger-facing value representing the number of whole
non-negative minutes from `referenceTime` until the applicable
arrival time.

The applicable arrival time shall be `predictedTime`
when available; otherwise `scheduledTime`.

This value is reference-time dependent and shall not
replace the underlying absolute event time.

## Calculation

For the purpose of this property, `selectedArrivalTime` shall be determined
as follows:

1. `predictedTime`, when available;
2. otherwise `scheduledTime`.

`observedTime` shall not be used to calculate passenger-facing
`minutesUntilArrival`.

`minutesUntilArrival` shall be calculated as the duration from
`referenceTime` to `selectedArrivalTime`, expressed in whole minutes.

The calculation shall use offset-aware ISO 8601 date-time values on a common
time axis. Both `referenceTime` and `selectedArrivalTime` shall include either
a UTC offset or `Z`.

A positive duration shall be truncated toward zero after conversion to
minutes. For example, 59 seconds shall produce `0`, and 119 seconds shall
produce `1`.

If `selectedArrivalTime` is earlier than `referenceTime`, the value shall be
reported as `0`.

A value of `0` therefore indicates that the selected arrival time is less than
one whole minute after `referenceTime`, is equal to `referenceTime`, or has
already passed.

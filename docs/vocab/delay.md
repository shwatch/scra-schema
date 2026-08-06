# delay

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/delay
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`, `MonitoredVehicleJourney`

## Value

Duration, normally expressed in seconds with NGSI-LD `unitCode` `SEC`

## Definition

`delay` shall represent the temporal difference between the applicable
operational event time and the applicable baseline time.

For a `PassingEvent`, the applicable operational event time shall be
`observedTime` when available; otherwise `predictedTime`.

The applicable baseline time shall be `scheduledTime` when available;
otherwise it shall be a baseline time defined by the applicable profile.

A positive value indicates that the event occurred or is expected to occur
later than the baseline time.

A negative value indicates that the event occurred or is expected to occur
earlier than the baseline time.

A value of zero indicates no difference from the baseline time.

The value shall be expressed in whole seconds using NGSI-LD `unitCode` `SEC`.

## Calculation

For the purpose of this property, `applicableOperationalTime` shall be
determined as follows:

1. `observedTime`, when available;
2. otherwise `predictedTime`.

`applicableBaselineTime` shall be determined as follows:

1. `scheduledTime`, when available;
2. otherwise a baseline time defined by the applicable profile.

`delay` shall be calculated as:

```text
applicableOperationalTime - applicableBaselineTime
```

The result shall be expressed in whole seconds using NGSI-LD `unitCode`
`SEC`.

A positive result indicates delay, a negative result indicates an early
event, and zero indicates that the event is on time relative to the
applicable baseline.

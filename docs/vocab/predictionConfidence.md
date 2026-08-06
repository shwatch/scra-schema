# predictionConfidence

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/predictionConfidence
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Definition

`predictionConfidence` shall represent the producer's assessment of the
reliability of a predicted operational time.

It applies only to predicted values such as `predictedTime` and related
predicted arrival estimates.

It shall not be interpreted as a confidence associated with
`scheduledTime` or `observedTime`.

The confidence assessment is qualitative rather than probabilistic.

Absence of this property means that confidence is not stated or is unknown;
it shall not be interpreted as the value `none`.

## Value Space

The normative values are:

- `high`: Sufficient operational evidence supports the prediction.
- `medium`: Operational evidence supports the prediction but with noticeable
  uncertainty.
- `low`: Only limited operational evidence supports the prediction.
- `none`: The producer explicitly indicates that no confidence assessment
  supports the prediction.

An absent `predictionConfidence` property means that confidence is not stated
or is unknown. Absence shall not be interpreted as `none`.

Profiles may define additional confidence values. An additional value should
provide a documented mapping to one of the normative values above.

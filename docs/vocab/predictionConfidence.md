# predictionConfidence

## Status

Draft

## IRI

```text
https://scra-schema.org/vocab/predictionConfidence
```

## Kind

NGSI-LD Property

## Domain

`PassingEvent`

## Definition

Describes the assessed confidence in a predicted time or related arrival
estimate.

The applicable profile shall define the value space. For the initial Mobility
MCP proof of concept, the values may be:

- `high`
- `medium`
- `low`
- `none`

Absence of this property means confidence is not stated; it shall not be
interpreted as `none`.

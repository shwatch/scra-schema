# currentPoint

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/currentPoint
```

## Kind

NGSI-LD Relationship

## Domain

`MonitoredVehicleJourney`

## Range

`PointInJourneyPattern`

## Normative Declaration

`https://scra-schema.org/vocab/currentPoint` is the canonical SCRA vocabulary IRI for the `currentPoint` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `currentPoint` SHALL expand to this IRI through the SCRA
Mobility Context.

## Definition

Relates a MonitoredVehicleJourney to the current or next PointInJourneyPattern used to express monitored progress.

## Semantic Constraint

The relationship identifies a logical point in the journey pattern and SHALL NOT be used as a substitute for stop identity.

## Semantic Basis

The domain, range, and semantic distinction are aligned with the normative SCRA
Semantic Models and the NGSI-LD semantic mapping.

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

© SCRA Project

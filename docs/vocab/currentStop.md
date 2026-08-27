# currentStop

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/currentStop
```

## Kind

NGSI-LD Relationship

## Domain

`MonitoredVehicleJourney`

## Range

`PublicTransportStop`

## Normative Declaration

`https://scra-schema.org/vocab/currentStop` is the canonical SCRA vocabulary IRI for the `currentStop` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `currentStop` SHALL expand to this IRI through the SCRA
Mobility Context.

## Definition

Relates a MonitoredVehicleJourney to the current or next operational PublicTransportStop associated with monitored progress.

## Semantic Constraint

This relationship is distinct from currentPoint because a PointInJourneyPattern and a PublicTransportStop are not semantically equivalent.

## Semantic Basis

The domain, range, and semantic distinction are aligned with the normative SCRA
Semantic Models and the NGSI-LD semantic mapping.

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

© SCRA Project

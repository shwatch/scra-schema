# operatedByVehicle

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/operatedByVehicle
```

## Kind

NGSI-LD Relationship

## Domain

`MonitoredVehicleJourney`

## Range

`PhysicalVehicle`

## Normative Declaration

`https://scra-schema.org/vocab/operatedByVehicle` is the canonical SCRA vocabulary IRI for the `operatedByVehicle` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `operatedByVehicle` SHALL expand to this IRI through the SCRA
Mobility Context.

## Definition

Relates a MonitoredVehicleJourney to the PhysicalVehicle currently operating the monitored journey.

## Semantic Constraint

This observed operational relationship is distinct from DatedVehicleJourney.assignedVehicle, which represents planned or assigned vehicle linkage.

## Semantic Basis

The domain, range, and semantic distinction are aligned with the normative SCRA
Semantic Models and the NGSI-LD semantic mapping.

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

© SCRA Project

# serviceCalendar

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/serviceCalendar
```

## Kind

NGSI-LD Relationship

## Domain

`ServiceJourney / OperatingDay`

## Range

`ServiceCalendar`

## Normative Declaration

`https://scra-schema.org/vocab/serviceCalendar` is the canonical SCRA vocabulary IRI for the `serviceCalendar` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `serviceCalendar` SHALL expand to this IRI through the SCRA
Mobility Context.

## Definition

Relates a ServiceJourney or OperatingDay to the ServiceCalendar that defines or governs its calendar applicability.

## Semantic Constraint

The range SHALL be ServiceCalendar only. DayType applicability SHALL be represented by the separate dayType relationship.

## Semantic Basis

The domain, range, and semantic distinction are aligned with the normative SCRA
Semantic Models and the NGSI-LD semantic mapping.

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

© SCRA Project

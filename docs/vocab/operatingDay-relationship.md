# operatingDay

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/operatingDay
```

## Kind

NGSI-LD Relationship

## Domain

`DatedVehicleJourney`

## Range

`OperatingDay`

## Normative Declaration

`https://scra-schema.org/vocab/operatingDay` is the canonical SCRA vocabulary IRI for the
`operatingDay` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `operatingDay` SHALL expand to this IRI through the
SCRA Mobility Context.

## Definition

Relates a DatedVehicleJourney to the OperatingDay on which that dated operation occurs.

The relationship object SHALL be the URI of the applicable `OperatingDay`
Entity.

## Semantic Basis

The domain and range above are derived from the normative SCRA semantic models
and the NGSI-LD mapping for the READY relationship set. This declaration
assigns the stable relationship IRI; it does not replace those semantic
definitions.

## JSON-LD

With the SCRA Mobility Context, the compact relationship term
`operatingDay` expands to:

```text
https://scra-schema.org/vocab/operatingDay
```

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

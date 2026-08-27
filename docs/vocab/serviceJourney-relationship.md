# serviceJourney

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/serviceJourney
```

## Kind

NGSI-LD Relationship

## Domain

`DatedVehicleJourney`

## Range

`ServiceJourney`

## Normative Declaration

`https://scra-schema.org/vocab/serviceJourney` is the canonical SCRA vocabulary IRI for the
`serviceJourney` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `serviceJourney` SHALL expand to this IRI through the
SCRA Mobility Context.

## Definition

Relates a DatedVehicleJourney to the planned ServiceJourney that it instantiates for an operating day.

The relationship object SHALL be the URI of the applicable `ServiceJourney`
Entity.

## Semantic Basis

The domain and range above are derived from the normative SCRA semantic models
and the NGSI-LD mapping for the READY relationship set. This declaration
assigns the stable relationship IRI; it does not replace those semantic
definitions.

## JSON-LD

With the SCRA Mobility Context, the compact relationship term
`serviceJourney` expands to:

```text
https://scra-schema.org/vocab/serviceJourney
```

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

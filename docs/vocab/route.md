# route

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/route
```

## Kind

NGSI-LD Relationship

## Domain

`ServiceJourney`, `JourneyPattern`

## Range

`PublicTransportRoute`

## Normative Declaration

`https://scra-schema.org/vocab/route` is the canonical SCRA vocabulary IRI for the
`route` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `route` SHALL expand to this IRI through the
SCRA Mobility Context.

## Definition

Relates a ServiceJourney or JourneyPattern to its applicable PublicTransportRoute.

The relationship object SHALL be the URI of the applicable `PublicTransportRoute`
Entity.

## Semantic Basis

The domain and range above are derived from the normative SCRA semantic models
and the NGSI-LD mapping for the READY relationship set. This declaration
assigns the stable relationship IRI; it does not replace those semantic
definitions.

## JSON-LD

With the SCRA Mobility Context, the compact relationship term
`route` expands to:

```text
https://scra-schema.org/vocab/route
```

## References

- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

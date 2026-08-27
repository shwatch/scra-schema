# ServiceJourney

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/ServiceJourney
```

## Kind

Entity Type / Semantic Class

## Normative Declaration

`https://scra-schema.org/vocab/ServiceJourney` is the canonical SCRA vocabulary IRI for the
`ServiceJourney` semantic class.

Conforming implementations that represent the SCRA `ServiceJourney` class as an
IRI SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `ServiceJourney` SHALL expand to this IRI through the
SCRA Mobility Context.

## Semantic Definition

The semantic meaning, identity rules, relationships, and constraints of this
term are defined by the [ServiceJourney semantic model](../models/ServiceJourney.md).

This vocabulary declaration assigns the stable SCRA IRI. It does not replace or
duplicate the semantic model.

## JSON-LD

With the SCRA Mobility Context:

```json
{
  "@context": "https://scra-schema.org/contexts/mobility.jsonld",
  "type": "ServiceJourney"
}
```

the compact term `ServiceJourney` expands to:

```text
https://scra-schema.org/vocab/ServiceJourney
```

## References

- [ServiceJourney semantic model](../models/ServiceJourney.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

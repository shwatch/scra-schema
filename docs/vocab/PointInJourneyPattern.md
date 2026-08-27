# PointInJourneyPattern

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/PointInJourneyPattern
```

## Kind

Entity Type / Semantic Class

## Normative Declaration

`https://scra-schema.org/vocab/PointInJourneyPattern` is the canonical SCRA vocabulary IRI for the
`PointInJourneyPattern` semantic class.

Conforming implementations that represent the SCRA `PointInJourneyPattern` class as an
IRI SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `PointInJourneyPattern` SHALL expand to this IRI through the
SCRA Mobility Context.

## Semantic Definition

The semantic meaning, identity rules, relationships, and constraints of this
term are defined by the [PointInJourneyPattern semantic model](../models/PointInJourneyPattern.md).

This vocabulary declaration assigns the stable SCRA IRI. It does not replace or
duplicate the semantic model.

## JSON-LD

With the SCRA Mobility Context:

```json
{
  "@context": "https://scra-schema.org/contexts/mobility.jsonld",
  "type": "PointInJourneyPattern"
}
```

the compact term `PointInJourneyPattern` expands to:

```text
https://scra-schema.org/vocab/PointInJourneyPattern
```

## References

- [PointInJourneyPattern semantic model](../models/PointInJourneyPattern.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

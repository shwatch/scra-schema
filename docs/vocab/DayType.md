# DayType

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/DayType
```

## Kind

Entity Type / Semantic Class

## Normative Declaration

`https://scra-schema.org/vocab/DayType` is the canonical SCRA vocabulary IRI for the
`DayType` semantic class.

Conforming implementations that represent the SCRA `DayType` class as an
IRI SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `DayType` SHALL expand to this IRI through the
SCRA Mobility Context.

## Semantic Definition

The semantic meaning, identity rules, relationships, and constraints of this
term are defined by the [DayType semantic model](../models/DayType.md).

This vocabulary declaration assigns the stable SCRA IRI. It does not replace or
duplicate the semantic model.

## JSON-LD

With the SCRA Mobility Context:

```json
{
  "@context": "https://scra-schema.org/contexts/mobility.jsonld",
  "type": "DayType"
}
```

the compact term `DayType` expands to:

```text
https://scra-schema.org/vocab/DayType
```

## References

- [DayType semantic model](../models/DayType.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

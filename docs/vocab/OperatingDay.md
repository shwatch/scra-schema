# OperatingDay

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/OperatingDay
```

## Kind

Entity Type / Semantic Class

## Normative Declaration

`https://scra-schema.org/vocab/OperatingDay` is the canonical SCRA vocabulary IRI for the
`OperatingDay` semantic class.

Conforming implementations that represent the SCRA `OperatingDay` class as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `OperatingDay` SHALL expand to this IRI through the SCRA
Mobility Context.

## Semantic Definition

The semantic meaning, identity rules, relationships, and constraints of this
term are defined by the [OperatingDay semantic model](../models/OperatingDay.md).

This vocabulary declaration assigns the stable SCRA IRI. It does not replace
or duplicate the semantic model.

## JSON-LD

With the SCRA Mobility Context:

```json
{
  "@context": "https://scra-schema.org/contexts/mobility.jsonld",
  "type": "OperatingDay"
}
```

the compact term `OperatingDay` expands to:

```text
https://scra-schema.org/vocab/OperatingDay
```

## References

- [OperatingDay semantic model](../models/OperatingDay.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

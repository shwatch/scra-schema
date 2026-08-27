# DatedVehicleJourney

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/DatedVehicleJourney
```

## Kind

Entity Type / Semantic Class

## Normative Declaration

`https://scra-schema.org/vocab/DatedVehicleJourney` is the canonical SCRA vocabulary IRI for the
`DatedVehicleJourney` semantic class.

Conforming implementations that represent the SCRA `DatedVehicleJourney` class as an
IRI SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `DatedVehicleJourney` SHALL expand to this IRI through the
SCRA Mobility Context.

## Semantic Definition

The semantic meaning, identity rules, relationships, and constraints of this
term are defined by the [DatedVehicleJourney semantic model](../models/DatedVehicleJourney.md).

This vocabulary declaration assigns the stable SCRA IRI. It does not replace or
duplicate the semantic model.

## JSON-LD

With the SCRA Mobility Context:

```json
{
  "@context": "https://scra-schema.org/contexts/mobility.jsonld",
  "type": "DatedVehicleJourney"
}
```

the compact term `DatedVehicleJourney` expands to:

```text
https://scra-schema.org/vocab/DatedVehicleJourney
```

## References

- [DatedVehicleJourney semantic model](../models/DatedVehicleJourney.md)
- [SCRA Mobility Vocabulary](index.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

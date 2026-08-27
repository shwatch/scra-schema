# operator

## Status

Normative

## IRI

```text
https://scra-schema.org/vocab/operator
```

## Kind

NGSI-LD Relationship

## Domain

`PhysicalVehicle`, `ServiceJourney`

## Range

`Organisation`

## Normative Declaration

`https://scra-schema.org/vocab/operator` is the canonical SCRA vocabulary IRI for the `operator` relationship.

Conforming implementations that represent this SCRA relationship as an IRI
SHALL use this IRI unless a specification profile explicitly defines an
equivalent external vocabulary term and declares the mapping.

The compact JSON-LD term `operator` SHALL expand to this IRI through the SCRA
Mobility Context.

## Definition

Relates a PhysicalVehicle or ServiceJourney to the Organisation that performs
the operator role for that entity in the applicable semantic context.

## Semantic Constraints

`operator` represents a role relationship, not a separate Operator entity type.

For `ServiceJourney`, the relationship represents the planned operator of the
scheduled service.

For `PhysicalVehicle`, the relationship represents the Organisation that
operates the vehicle in the applicable operational context.

The same Organisation may also act as a transport authority, owner, manager, or
data publisher. Those roles SHALL NOT be inferred from `operator` alone.

`DatedVehicleJourney` is not part of the normative domain in this phase because
its current semantic model does not define a dedicated operator relationship.

## Mapping Guidance

A GTFS `agency.txt` record may supply the Organisation referenced by `operator`
where the applicable GTFS semantics justify the operator role.

The existence of a GTFS agency record SHALL NOT by itself imply that the
Organisation is simultaneously operator, authority, owner, and publisher.

## References

- [Organisation semantic model](../models/Organisation.md)
- [ServiceJourney semantic model](../models/ServiceJourney.md)
- [PhysicalVehicle semantic model](../models/PhysicalVehicle.md)
- [SCRA NGSI-LD mapping](../mappings/ngsi-ld.md)
- [SCRA GTFS Static mapping](../mappings/gtfs-static.md)
- [SCRA Mobility JSON-LD Context](../contexts/mobility.jsonld)

© SCRA Project

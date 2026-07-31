# ADR-0001: Use PublicTransportRoute as the Semantic Route Entity Type

## Status

Accepted

## Context

SCRA and Mobility MCP require a semantic representation of public transport
routes that is suitable for AI-ready data exchange and is not unnecessarily
coupled to a particular source data format.

GTFS defines a route as a group of trips that are displayed to riders as a
single service. Route information is represented in `routes.txt`.

Smart Data Models provides the GTFS-oriented `GtfsRoute` entity type.
Its attributes intentionally preserve GTFS semantics. For example,
`shortName` corresponds to GTFS `route_short_name`, and `routeType`
corresponds to GTFS `route_type`.

Smart Data Models also provides `PublicTransportRoute`, defined as a
generic public transport route.

Mobility MCP may receive mobility information from GTFS, GTFS-Realtime,
provider APIs, municipal systems, and other mobility standards. Therefore,
the semantic layer should represent the transport-domain concept itself
rather than preserve the structure of a particular input format.

## Decision

SCRA and Mobility MCP will use the Smart Data Models
`PublicTransportRoute` entity type as the preferred semantic representation
of a public transport route.

`GtfsRoute` will not be used as the canonical semantic entity type merely
because source data originates from GTFS.

GTFS-specific structures and coded values SHALL be interpreted by the
adapter or semantic mapping layer and converted into generic semantic
properties and relationships where suitable existing vocabulary is
available.

The source-specific identifier, such as GTFS `route_id`, SHALL be used in
the construction of a stable NGSI-LD Entity identifier according to the
SCRA identity rules. The precise identifier construction rule is defined
separately from this ADR.

## Initial Mapping Candidates

The following mappings are candidates based on the currently available
Smart Data Models vocabulary:

| GTFS field | Candidate semantic representation |
| --- | --- |
| `route_id` | Source identifier used to construct the NGSI-LD Entity `id` |
| `route_short_name` | `shortRouteCode` |
| `route_long_name` | `name` |
| `route_desc` | `description` |
| `route_type` | `transportationType`, after semantic conversion |
| `route_color` | `routeColor` |
| `route_text_color` | `routeTextColor` |

These property mappings are not made normative by this ADR.

Each mapping SHALL be verified individually against the semantics,
value constraints, and intended usage of the target vocabulary before
being adopted into the SCRA vocabulary or reference implementation.

Relationships such as the relationship between a route and its operating
agency will be evaluated separately. This ADR does not establish the
canonical relationship vocabulary for that association.

## Rationale

`GtfsRoute` is appropriate when the objective is to represent GTFS data
while retaining GTFS-specific concepts and terminology.

The SCRA semantic layer has a broader objective.

Equivalent transport concepts originating from different source systems
should be capable of converging on the same semantic representation.

Conceptually:

    GTFS routes.txt -----------+
                               |
    Provider API --------------+--> PublicTransportRoute
                               |
    Municipal transport data --+
                               |
    Other mobility standards --+

This separates source-format interoperability from semantic
interoperability.

GTFS remains an important source standard, but it does not define the
canonical semantic model.

This approach also reduces the amount of source-format knowledge required
by AI systems consuming SCRA-compatible data. An AI system can reason about
a generic public transport route without first having to understand the
structure and coded values of GTFS.

## Consequences

- GTFS remains a supported source standard.
- GTFS-specific structures remain in the adapter and mapping layers.
- `PublicTransportRoute` becomes the preferred semantic entity type for
  public transport routes.
- Other source standards can map directly to the same semantic entity type.
- GTFS coded values SHOULD be converted to semantic values where an
  appropriate target vocabulary exists.
- Relationships to Route entities SHOULD use NGSI-LD Relationships rather
  than reproducing source-format nesting or foreign-key structures.
- Existing vocabularies SHOULD be reused when their semantics adequately
  represent the required concept.
- SCRA-specific vocabulary terms SHOULD be introduced only when an
  adequate existing term cannot be identified.
- Individual attribute mappings remain subject to separate semantic
  verification.

## Evidence

### GTFS

The GTFS Schedule Reference defines `routes.txt` and its fields including
`route_id`, `agency_id`, `route_short_name`, `route_long_name`,
`route_desc`, `route_type`, `route_color`, and `route_text_color`.

GTFS defines a route as a group of trips displayed to riders as a single
service.

### Smart Data Models: GtfsRoute

`GtfsRoute` preserves GTFS-oriented semantics. Its definitions explicitly
map attributes such as `shortName` and `routeType` to the corresponding
GTFS fields.

This makes it useful as a GTFS representation, but also couples the model
to GTFS terminology and value conventions.

### Smart Data Models: PublicTransportRoute

`PublicTransportRoute` is defined as a generic public transport route.

Its vocabulary includes generic route attributes such as `name`,
`description`, `routeCode`, `shortRouteCode`, `routeColor`,
`routeTextColor`, and `transportationType`.

This makes it a better candidate for the source-independent semantic layer.

### ETSI NGSI-LD

The NGSI-LD Information Model represents real-world concepts as Entities
with Properties and Relationships.

Domain-specific models may define domain entity types and relationships
while retaining the common NGSI-LD information model.

This supports the SCRA approach of representing Route, Trip, Stop, Vehicle,
and related concepts as semantic entities connected through NGSI-LD
Relationships.

## References

- GTFS Schedule Reference:
  https://gtfs.org/documentation/schedule/reference/

- Smart Data Models — PublicTransportRoute:
  https://smartdatamodels.org/dataModel.UrbanMobility/PublicTransportRoute

- Smart Data Models — GtfsRoute:
  https://fiware-datamodels.readthedocs.io/en/stable/UrbanMobility/GtfsRoute/doc/spec/index.html

- ETSI NGSI-LD:
  https://cim.etsi.org/NGSI-LD/official/
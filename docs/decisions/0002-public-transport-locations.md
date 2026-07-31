# ADR-0002: Represent Public Transport Locations as Semantic Entities

## Status

Accepted

## Context

SCRA and Mobility MCP require a source-independent semantic representation
of public transport locations.

GTFS represents several different kinds of physical transport locations in
`stops.txt`. The meaning of each record is determined in part by
`location_type`.

GTFS currently distinguishes:

- `0` — Stop / Platform
- `1` — Station
- `2` — Entrance / Exit
- `3` — Generic Node
- `4` — Boarding Area

These records do not all represent the same real-world concept.

Representing every record in `stops.txt` as a single semantic `Stop` entity
would preserve the structure of the GTFS source format but would lose
important distinctions between passenger boarding locations, stations,
entrances, internal navigation locations, and specific boarding positions.

SCRA therefore requires a semantic mapping from source-specific location
records to real-world transport location concepts.

## Decision

SCRA and Mobility MCP SHALL NOT treat a GTFS `stops.txt` record as the
canonical semantic entity merely because it originates from that file.

The GTFS `location_type` and related fields SHALL be interpreted by the
adapter or semantic mapping layer.

The resulting NGSI-LD entity SHALL represent the real-world transport
location concept identified by the source data.

For ordinary passenger boarding and alighting locations, Smart Data Models
`PublicTransportStop` is the preferred existing semantic entity type.

Stations and more detailed transport-location concepts SHALL be represented
as distinct semantic entities where the source data provides sufficient
information to make that distinction.

## Initial Semantic Mapping

The following mapping establishes the conceptual direction:

| GTFS `location_type` | GTFS meaning | Semantic concept |
| --- | --- | --- |
| `0` | Stop / Platform | `PublicTransportStop` |
| `1` | Station | Transport station / stop-place concept |
| `2` | Entrance / Exit | Stop Place Entrance |
| `3` | Generic Node | Access Space / navigation node |
| `4` | Boarding Area | Boarding Position / boarding-area concept |

This table defines semantic intent.

The exact vocabulary IRI for each concept SHALL be selected according to
the vocabulary reuse policy described below.

## Stop and Station Are Distinct Concepts

A public transport stop and a transport station SHALL NOT be treated as
synonymous concepts.

A `PublicTransportStop` represents a passenger boarding or alighting
location.

Such a stop MAY exist independently.

For example, an ordinary roadside bus stop does not require a parent
station.

A transport station represents a larger transport facility or stop-place
concept that may contain or relate multiple passenger boarding locations,
entrances, access spaces, and other transport-location entities.

Conceptually:

    TransportStation / StopPlace
              |
              +-- PublicTransportStop
              |
              +-- PublicTransportStop
              |
              +-- Entrance
              |
              +-- AccessSpace
              |
              +-- BoardingPosition
              |
              +-- NavigationPath

A station may support a single transport mode or multiple transport modes.

The semantic model SHALL NOT require a station to be a multimodal
interchange. However, the model SHALL permit multiple transport services,
routes, stops, and modes to be associated with the same station or
stop-place.

This capability is important for MaaS and multimodal journey applications.

## Parent and Containment Relationships

A `PublicTransportStop` MAY exist without a parent station.

Where a passenger boarding location belongs to a larger station or
stop-place, that relationship SHOULD be represented explicitly using an
NGSI-LD Relationship.

Source-specific foreign keys or nested structures SHOULD NOT be used as the
canonical semantic representation of that association.

The precise relationship vocabulary SHALL be verified separately before
being made normative.

## Transmodel and NeTEx

Transmodel defines established public-transport concepts for physical stop
and station structures, including concepts such as:

- STOP PLACE
- QUAY
- BOARDING POSITION
- STOP PLACE ENTRANCE
- ACCESS SPACE
- NAVIGATION PATH

NeTEx provides an exchange representation based on concepts from
Transmodel.

These concepts provide an established semantic basis for representing
complex transport facilities and passenger navigation.

SCRA SHOULD reuse the semantics of established transport-domain standards
rather than invent equivalent concepts unnecessarily.

## Vocabulary Reuse Policy

Vocabulary selection for SCRA SHALL follow this order of preference:

1. NGSI-LD core vocabulary
2. Suitable existing Smart Data Models vocabulary
3. Suitable concepts from established domain standards such as
   Transmodel / NeTEx
4. Other suitable established vocabularies
5. SCRA-defined vocabulary only where an adequate directly reusable
   vocabulary term is not available

The existence of a concept in an established conceptual or exchange
standard does not necessarily imply that a stable RDF or JSON-LD vocabulary
IRI is available for direct use in an NGSI-LD `@context`.

Where an established concept exists but no suitable stable and directly
reusable official JSON-LD/RDF IRI is available, SCRA MAY provide its own
stable vocabulary IRI for use in NGSI-LD.

In such cases, SCRA is not defining a new transport concept.

The SCRA vocabulary definition SHALL identify the established external
concept that provides the semantic basis for the SCRA term.

For example, subject to final vocabulary verification:

    https://scra-schema.org/vocab/StopPlace
    https://scra-schema.org/vocab/Quay
    https://scra-schema.org/vocab/BoardingPosition
    https://scra-schema.org/vocab/StopPlaceEntrance
    https://scra-schema.org/vocab/AccessSpace
    https://scra-schema.org/vocab/NavigationPath

These IRIs are illustrative candidates and are not made normative by this
ADR.

## GTFS Location Type

GTFS `location_type` is a source-format classification.

Where its semantic meaning has been fully represented by the resulting
entity type, SCRA does not require the original numeric `location_type`
value to serve as the primary semantic description.

For example:

    GTFS:
        location_type = 1

may be mapped to an entity whose type explicitly identifies it as a
transport station or stop-place.

The original GTFS value MAY still be retained as provenance or
source-specific information where required for traceability, debugging, or
round-trip conversion.

## Navigation and Accessibility

The separation of stations, stops, entrances, access spaces, boarding
positions, and navigation paths enables a semantic graph suitable for
passenger navigation.

Conceptually:

    Vehicle
       |
       | currentStop
       v
    PublicTransportStop
       |
       v
    TransportStation / StopPlace
       |
       +-- Entrance
       |
       +-- AccessSpace
       |
       +-- NavigationPath
       |
       +-- Quay / BoardingPosition

This structure can support future MaaS, interchange, accessibility, and
pedestrian-navigation use cases.

In particular, transport-location semantics SHOULD preserve information
that may be required to describe accessible movement between entrances,
station spaces, platforms, boarding locations, and connecting transport
services.

This ADR establishes the semantic structure only. It does not define an
accessibility routing algorithm.

## Rationale

GTFS `stops.txt` is a source representation containing several distinct
real-world concepts.

Using a single `Stop` entity type for all records would couple the semantic
model to the GTFS file structure and obscure distinctions already present
in the source standard.

Separating these concepts at the semantic layer allows data originating
from GTFS, NeTEx, provider APIs, municipal systems, and other mobility
standards to converge on equivalent real-world entities.

It also allows AI systems to reason about the meaning of a location
directly rather than first interpreting GTFS-specific numeric
classifications.

The use of established Transmodel concepts where appropriate reduces the
need for SCRA-specific transport concepts while allowing SCRA to provide
stable JSON-LD vocabulary IRIs when required for NGSI-LD interoperability.

## Consequences

- GTFS `stops.txt` SHALL NOT automatically map every record to one `Stop`
  entity type.
- `location_type` SHALL be interpreted during semantic mapping.
- Ordinary passenger boarding/alighting locations SHOULD use
  `PublicTransportStop` where its semantics are appropriate.
- Stations SHALL remain semantically distinct from passenger boarding
  locations.
- Stops MAY exist independently of stations.
- Relationships between transport locations SHOULD be represented using
  NGSI-LD Relationships.
- Detailed station structures MAY use concepts derived from Transmodel.
- SCRA-specific IRIs MAY be created where an established concept lacks a
  suitable directly reusable JSON-LD/RDF vocabulary IRI.
- Any SCRA term based on an external standard SHALL document its semantic
  source.
- Source-specific identifiers and classifications MAY be retained as
  provenance without defining the canonical semantic model.
- The resulting location graph SHOULD remain usable across transport modes
  and source standards.

## Evidence

### GTFS

The GTFS Schedule Reference defines multiple location categories within
`stops.txt` using `location_type`, including Stop/Platform, Station,
Entrance/Exit, Generic Node, and Boarding Area.

GTFS Pathways uses these location concepts to represent passenger movement
through transport facilities.

### Smart Data Models

Smart Data Models provides `PublicTransportStop` as a generic public
transport stop model, distinct from GTFS-specific models.

This provides an existing NGSI-LD-oriented vocabulary for ordinary public
transport boarding/alighting locations.

### Transmodel

Transmodel defines a detailed public transport stop-place model including
Stop Place, Quay, Boarding Position, Stop Place Entrance, Access Space,
and Navigation Path concepts.

These concepts provide an established semantic basis for complex station
and interchange structures.

### NeTEx

NeTEx provides a standardized public transport data exchange format based
on concepts from Transmodel and includes detailed stop-place and passenger
navigation structures.

### ETSI NGSI-LD

NGSI-LD provides Entity, Property, GeoProperty, and Relationship constructs
that allow these real-world transport concepts to be represented as linked
semantic entities.

## References

- GTFS Schedule Reference:
  https://gtfs.org/documentation/schedule/reference/

- GTFS Pathways:
  https://gtfs.org/getting-started/features/pathways/

- Smart Data Models:
  https://smartdatamodels.org/

- Transmodel:
  https://www.transmodel-cen.eu/

- NeTEx:
  https://www.transmodel-cen.eu/index.php/netex/

- ETSI NGSI-LD:
  https://cim.etsi.org/NGSI-LD/official/
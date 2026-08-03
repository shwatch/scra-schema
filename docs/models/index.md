# Semantic Models

## Status

Normative

---

## Purpose

SCRA Semantic Models define the canonical meanings of concepts used within
the Smart City Reference Architecture (SCRA).

Each semantic model represents a distinct concept independently of any
implementation technology.

Semantic models define **what a concept means**, not **how it is represented,
exchanged, stored, or exposed through an API**.

---

## Relationship to Other Specifications

The principal SCRA Schema specification layers are:

    Architecture Decision Records
                |
                v
    Semantic Modeling Principles
                |
                v
          Semantic Models
                |
                v
    Semantic Mapping Specifications
                |
                v
    NGSI-LD / JSON-LD / RDF / MCP

Architecture Decision Records explain **why** particular semantic decisions
were adopted.

Semantic Modeling Principles define the rules governing the development of
semantic models.

Semantic Models define **what** the canonical concepts mean and what
constitutes their semantic identity.

Semantic Mapping Specifications define **how** source standards and
implementation technologies correspond to those concepts.

---

## Core Public Transport Models

The initial SCRA public transport semantic model includes:

- [ServiceJourney](ServiceJourney.md)
- [DatedVehicleJourney](DatedVehicleJourney.md)
- [MonitoredVehicleJourney](MonitoredVehicleJourney.md)
- [PhysicalVehicle](PhysicalVehicle.md)
- [PassingEvent](PassingEvent.md)

The following additional concepts are established by Architecture Decision
Records and will receive normative semantic model pages:

- PublicTransportRoute
- PublicTransportStop
- TransportStation
- JourneyPattern
- PointInJourneyPattern

Additional models may be introduced as the specification expands into other
smart-city domains.

---

## Model Categories

### Planning Concepts

Planning concepts describe recurring or long-term transport plans.

Current planning concepts include:

- ServiceJourney
- PublicTransportRoute
- JourneyPattern
- PublicTransportStop

### Dated Operational Concepts

Dated operational concepts describe plans associated with a particular
operating day.

Current dated operational concepts include:

- DatedVehicleJourney

### Monitored Operational Concepts

Monitored operational concepts describe the observed, predicted, or reported
execution of transport operations.

Current monitored operational concepts include:

- MonitoredVehicleJourney
- PassingEvent

### Physical Concepts

Physical concepts describe persistent real-world transport assets and
locations.

Current physical concepts include:

- PhysicalVehicle
- TransportStation

---

## Principal Relationships

The principal journey relationships are:

    ServiceJourney
            |
            | has dated occurrence
            v
    DatedVehicleJourney
            |
            | is monitored as
            v
    MonitoredVehicleJourney
            |
            | is operated by
            v
    PhysicalVehicle

A MonitoredVehicleJourney may have multiple operational events:

    MonitoredVehicleJourney
            |
            | has passing event
            v
       PassingEvent
            |
            | occurs at
            v
    PublicTransportStop

A ServiceJourney may be associated with a route and normally follows a
journey pattern:

    PublicTransportRoute
            ^
            |
       ServiceJourney
            |
            v
       JourneyPattern
            |
            v
    PublicTransportStop

These relationships are conceptual and independent of any implementation
technology.

---

## Semantic Identity

Every normative semantic model shall contain an `Identity` section.

Semantic identity defines:

- the conditions under which two instances represent the same concept;
- the changes that do not alter that identity; and
- the changes that result in a distinct semantic instance.

Semantic identity is independent of source identifiers, database keys,
NGSI-LD Entity identifiers, URIs, and other implementation-specific
identifiers.

---

## Adoption of Existing Standards

SCRA Schema adopts existing semantic concepts whenever they accurately
represent the intended meaning.

For public transport, Transmodel is the principal domain reference model.

Other relevant standards and vocabularies may include:

- NeTEx
- SIRI
- NGSI-LD
- Smart Data Models
- Schema.org
- SOSA / SSN
- GTFS
- GTFS-Realtime

SCRA follows this order:

1. Adopt an existing concept.
2. Harmonize compatible concepts.
3. Extend an existing concept where necessary.
4. Create a new concept only when existing standards are insufficient.

---

© SCRA Project
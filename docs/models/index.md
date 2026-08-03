# Semantic Models

## Status

Normative

---

## Purpose

SCRA Semantic Models define the canonical meaning of concepts used within
the Smart City Reference Architecture (SCRA).

Each semantic model represents a single concept independently of any
implementation technology.

Semantic models define **what a concept is**, not **how it is represented**
or **how it is exchanged**.

---

## Relationship to Other Specifications

The relationship between the principal specifications of SCRA Schema is
illustrated below.

```
Architecture Decisions
        │
        ▼
Semantic Modeling Principles
        │
        ▼
Semantic Models
        │
        ▼
Semantic Mapping Specifications
        │
        ▼
NGSI-LD / JSON-LD / RDF / MCP
```

Architecture Decision Records explain **why** semantic concepts have been
adopted.

Semantic Models define **what** those concepts mean.

Semantic Mapping Specifications define **how** semantic concepts are mapped
to implementation technologies.

---

## Core Public Transport Models

The initial version of SCRA Schema defines the following public transport
semantic models.

- PublicTransportRoute
- PublicTransportStop
- ServiceJourney
- DatedVehicleJourney
- MonitoredVehicleJourney
- PhysicalVehicle
- EstimatedPassingTime

Additional semantic models will be introduced as new domains are
standardised.

---

## Design Principles

All semantic models shall conform to the
*Semantic Modeling Principles* specification.

Whenever possible, semantic concepts are adopted from existing
international standards such as Transmodel.

SCRA introduces new semantic concepts only when existing standards are
insufficient.

---

## Model Relationships

The principal semantic relationships are illustrated below.

```
PhysicalVehicle
        │ operates
        ▼
MonitoredVehicleJourney
        │ implements
        ▼
ServiceJourney
        │ follows
        ▼
PublicTransportRoute

EstimatedPassingTime
        │ occurs at
        ▼
PublicTransportStop
```

These relationships are conceptual and independent of any implementation
technology.

---

© SCRA Project

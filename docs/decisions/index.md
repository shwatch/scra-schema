# Architecture Decision Records

Architecture Decision Records (ADRs) document the important architectural
and semantic decisions made during the development of SCRA.

Rather than describing only *what* the specification contains, ADRs explain
*why* particular design choices were adopted, which alternatives were
considered, and the reasoning behind each decision.

---

## ADR Index

| ADR | Title | Status |
|-----|-------|--------|
| [ADR-0001](0001-public-transport-route.md) | Use `PublicTransportRoute` as the Semantic Route Entity Type | Accepted |
| [ADR-0002](0002-public-transport-locations.md) | Represent Public Transport Locations Semantically | Accepted |
| [ADR-0003](0003-service-journey.md) | Represent Scheduled Public Transport Operations as Service Journeys | Accepted |
| [ADR-0004](0004-realtime-vehicle-journeys.md) | Model Real-Time Operations with Dated and Monitored Vehicle Journeys | Accepted |
| [ADR-0005](0005-physical-vehicle.md) | Represent Vehicles as Physical Transport Entities | Accepted |
| [ADR-0006](0006-realtime-passing-times.md) | Represent Real-Time Stop Events as Passing Times | Accepted |

---

## Purpose

Architecture Decision Records provide:

- architectural rationale
- semantic consistency
- long-term maintainability
- historical traceability
- transparent design decisions

Each ADR represents one independent architectural decision.

Later ADRs may supersede earlier decisions while preserving the historical
record.

---

© SCRA Project
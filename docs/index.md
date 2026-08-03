# SCRA Schema

> Semantic Specification for the Smart City Reference Architecture (SCRA)

SCRA Schema provides the semantic specification for the Smart City Reference Architecture (SCRA).

It defines the canonical semantic foundation for interoperable smart cities,
mobility systems, digital twins, and AI-ready information exchange.

Rather than defining implementation-specific APIs or software platforms,
SCRA Schema establishes common semantic concepts, vocabularies, and
relationships that enable heterogeneous systems to exchange information
through shared meaning.

The specification is designed to align with linked data principles and
international standards, including NGSI-LD, RDF, JSON-LD, GTFS,
GTFS-Realtime, and Transmodel.

---

## Scope

SCRA Schema defines canonical semantic concepts, vocabularies, and semantic
relationships for interoperable smart-city information exchange.

The specification focuses on semantic interoperability and does not define
implementation-specific solutions.

Specifically, SCRA Schema does not define:

- Communication protocols
- APIs
- Software implementations
- Broker architectures
- Deployment models
- Internal system designs

Those concerns are intentionally delegated to implementation-specific
architectures and standards.

This separation allows the semantic foundation to remain stable while
supporting multiple implementation technologies, including NGSI-LD,
JSON-LD, RDF, Model Context Protocol (MCP), and future interoperability
frameworks.

---

## Purpose

SCRA Schema provides the semantic foundation of SCRA.

Its objectives are:

- Define canonical semantic models
- Provide machine-readable semantic models and vocabularies
- Support AI interoperability
- Enable cross-domain semantic consistency
- Promote long-term interoperability

---

## Vision

Modern cities operate through many independent information systems.

Although these systems frequently describe the same real-world entities,
they often use incompatible data structures and proprietary interfaces.

SCRA establishes a common semantic layer that allows information to be
shared, integrated, and interpreted consistently across domains.

The long-term vision is an open semantic ecosystem supporting public
agencies, private operators, digital twins, mobility services, and AI
applications.

---

## Documentation Structure

The documentation is organised into several complementary sections.

### Specifications

Normative specification documents describing the architecture and semantic
framework.

### Architecture Decision Records (ADR)

Design decisions explaining why particular semantic approaches were adopted.

### Semantic Models

Canonical definitions of entities, properties, and relationships.

### JSON-LD Contexts

Machine-readable linked-data vocabularies.

### Examples

Reference examples demonstrating practical semantic modelling.

---

## Current Status

SCRA Schema is under active development.

The current work focuses on semantic models for public transport,
real-time mobility information, and AI interoperability.

Future work will extend the semantic framework to additional smart-city
domains.

---

## Repository

The source repository is available on GitHub.

<https://github.com/shwatch/scra-schema>

---

© SCRA Project

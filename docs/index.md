# SCRA

> Semantic City Reference Architecture

SCRA is an open semantic specification for interoperable smart cities,
mobility systems, digital twins, and AI-ready information exchange.

The project defines a common semantic foundation based on linked data
principles, enabling heterogeneous urban systems to exchange information
through shared meanings rather than source-specific data structures.

SCRA is designed to be compatible with NGSI-LD while remaining independent
of any specific implementation platform or software product.

---

## Vision

Modern cities operate through many independent information systems.

Although these systems often describe the same real-world objects,
they frequently use incompatible data models and proprietary interfaces.

SCRA provides a common semantic layer that enables information to be shared,
integrated, and interpreted consistently across domains.

The long-term objective is to establish an open semantic foundation that
supports interoperability between public agencies, private operators,
digital twins, mobility systems, and AI applications.

---

## Design Principles

SCRA is developed according to the following principles.

### Semantic First

The semantic meaning of information is defined before implementation.

Source-specific structures are mapped into canonical semantic concepts rather
than being reproduced directly.

### Open Standards

Existing international standards are reused whenever their semantics are
compatible with the intended meaning.

Examples include:

- NGSI-LD
- RDF
- JSON-LD
- GTFS
- GTFS-Realtime
- Transmodel

### AI-Ready

Semantic models should support both human understanding and machine reasoning.

The specification is intended to serve as a common semantic foundation for
AI agents as well as conventional information systems.

### Extensible

The core semantic model should remain stable while allowing domain-specific
extensions without breaking interoperability.

---

## Documentation

The specification is organised into several complementary sections.

### Specifications

Normative documents describing the overall architecture and semantic
framework.

### Architecture Decision Records (ADR)

Design decisions documenting why particular semantic approaches have been
adopted.

### Semantic Models

Canonical definitions of entities, properties, and relationships.

### JSON-LD Contexts

Machine-readable semantic vocabularies for linked data applications.

### Examples

Illustrative examples demonstrating practical implementation patterns.

---

## Current Status

SCRA is currently under active development.

The initial work focuses on semantic modelling for public transport,
real-time mobility information, and AI interoperability.

Future work will extend the semantic framework to additional smart-city
domains.

---

## Repository

GitHub Repository

https://github.com/shwatch/scra-schema

---

© SCRA Project
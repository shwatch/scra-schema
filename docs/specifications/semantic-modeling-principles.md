# Semantic Modeling Principles

## Status

Normative

---

## 1. Purpose

This document defines the principles used to develop semantic models within
SCRA Schema.

These principles ensure that semantic models remain stable, interoperable,
and independent of implementation technologies while supporting multiple
data exchange standards and AI applications.

---

## 2. Scope

These principles apply to all semantic models defined by SCRA Schema.

They do not define implementation-specific representations such as JSON,
JSON-LD, RDF serializations, APIs, communication protocols, or software
architectures.

---

## 3. Fundamental Principles

### 3.1 Semantics First

Semantic models shall define the meaning of concepts independently of their
technical representation.

Implementations may evolve over time, but the underlying semantic meaning
should remain stable.

---

### 3.2 Technology Independence

Semantic models shall not depend on any specific implementation technology.

In particular, semantic models shall not be defined in terms of:

- NGSI-LD entities
- JSON or JSON-LD structures
- RDF serialization
- APIs
- Message formats
- Database schemas
- Software implementations

These technologies are mappings of the semantic model, not the semantic
model itself.

---

### 3.3 Canonical Concepts

Each semantic model shall represent a single canonical concept.

Different implementation technologies may represent the same concept in
different ways, but the semantic definition shall remain unique.

---

### 3.4 Semantic Identity

Each semantic model shall explicitly define its semantic identity.

Semantic identity specifies the conditions under which two instances
represent the same real-world concept.

Semantic identity is independent of identifiers, implementation
technologies, and data representations.

Changes to operational state, implementation details, source-specific
identifiers, or technical representations shall not alter semantic identity
unless the underlying real-world concept itself changes.

The `Identity` section of each semantic model shall identify:

- the conditions that establish identity;
- the changes that do not alter identity; and
- where relevant, the changes that result in a distinct semantic instance.

Semantic identity does not prescribe the construction of an implementation
identifier, URI, URN, database key, or NGSI-LD Entity identifier.

Those identifier construction rules shall be defined separately in the
relevant mapping or implementation specification.

---

### 3.5 Adopt Existing Standards

SCRA Schema shall adopt existing international semantic standards whenever
they accurately represent the intended concept.

Existing semantic definitions shall be preferred over introducing new
concepts.

New semantic concepts shall only be introduced when existing standards are
insufficient to express the required meaning.

The objective of SCRA Schema is to harmonize existing semantic standards
rather than to replace or compete with them.

Typical reference standards include:

- Transmodel
- NGSI-LD Cross-Domain Ontology
- Smart Data Models
- Schema.org
- SOSA / SSN
- GTFS
- GTFS-Realtime

---

### 3.6 Reuse Before Creation

SCRA Schema reuses existing international semantic concepts whenever they
accurately represent the intended meaning.

When multiple standards define equivalent or overlapping concepts, SCRA
Schema shall select the concept that provides the most stable, precise, and
widely accepted semantic definition for the intended scope.

Where appropriate, SCRA Schema may harmonize concepts from multiple
standards while preserving their original semantic meanings.

New semantic concepts shall only be introduced when existing standards are
insufficient.

---

### 3.7 Separation of Semantic Models and Mappings

Semantic models define meaning.

Mappings define how those meanings are represented within individual source
standards, exchange formats, implementation technologies, and software
interfaces.

Examples include:

- GTFS → Semantic Model
- GTFS-Realtime → Semantic Model
- NeTEx → Semantic Model
- SIRI → Semantic Model
- Semantic Model → NGSI-LD
- Semantic Model → JSON-LD
- Semantic Model → RDF
- Semantic Model → MCP

Mappings shall not modify the normative semantic definition of a concept.

Where a source standard does not provide sufficient information to establish
a semantic distinction, a mapping shall not introduce certainty that is not
present in the source data.

Mapping specifications shall clearly distinguish normative semantic
requirements from informative source-specific guidance.

---

## 4. Required Semantic Model Structure

Each normative semantic model shall contain, at minimum, the following
sections:

- `Status`
- `Definition`
- `Identity`
- `Motivation`
- `Characteristics`
- `Relationships`
- `Typical Properties`
- `Origin`
- `Mapping Guidance`
- `See Also`

The `Definition` section specifies what the concept means.

The `Identity` section specifies what constitutes the same semantic instance.

The `Origin` section identifies the existing standards or semantic concepts
from which the model is adopted, derived, harmonized, or extended.

The `Mapping Guidance` section is informative unless explicitly stated
otherwise.

Additional sections may be included where necessary for a particular
concept.

---

## 5. Relationship to SCRA

SCRA Schema provides the semantic foundation of the Smart City Reference
Architecture (SCRA).

Semantic models defined by SCRA Schema are intended to support multiple
reference implementations and interoperability frameworks while maintaining
consistent meanings across domains.

SCRA Schema defines semantic meaning independently of the technologies used
to exchange, store, query, or expose that meaning.

---

## 6. Conformance

A semantic model conforms to these principles when it:

- defines semantic meaning independently of implementation technology;
- represents a single canonical concept;
- explicitly defines its semantic identity;
- clearly distinguishes normative semantic definitions from informative
  mappings;
- identifies the origin of adopted, derived, harmonized, or extended
  concepts;
- adopts existing international semantic concepts whenever appropriate;
- introduces new semantic concepts only when existing standards are
  insufficient; and
- conforms to the required semantic model structure defined in this
  document.

---

## 7. Design Philosophy

SCRA Schema is not intended to replace existing semantic standards.

Its purpose is to adopt, harmonize, integrate, and extend existing standards
where necessary while preserving their original semantic meanings whenever
possible.

The preferred order of semantic model development is:

1. Adopt an existing semantic concept when it accurately represents the
   intended meaning.
2. Harmonize existing concepts when multiple standards provide compatible or
   complementary definitions.
3. Extend an existing concept only where a documented semantic gap remains.
4. Create a new semantic concept only when existing standards cannot
   adequately represent the intended meaning.

Any SCRA-specific concept shall document:

- the semantic gap that required its introduction;
- the existing standards that were evaluated;
- its relationship to those standards; and
- the reason why adoption, harmonization, or extension was insufficient.

---

© SCRA Project
# Organisation

## Status

Normative

---

## Definition

An Organisation represents an organisational entity that may perform one or
more roles in the planning, provision, management, operation, ownership,
regulation, or publication of public transport services and data.

Organisation is a role-neutral semantic class.

An Organisation SHALL NOT be assumed to be a transport operator merely because
it appears in a source dataset such as GTFS `agency.txt`.

---

## Identity

The semantic identity of an Organisation is established by the organisational
entity itself within the applicable identifier and source-authority scope.

An Organisation remains the same semantic instance when:

- it performs different transport roles;
- it operates different ServiceJourneys;
- it manages or operates different PhysicalVehicles;
- it acts as an authority, owner, publisher, or operator in different contexts;
- source records or identifiers are revised without changing the underlying
  organisation.

Where source identifiers are only locally unique, the applicable mapping
profile SHALL preserve the source or authority scope needed to avoid identity
collisions.

---

## Motivation

Public transport data frequently represents organisations through source
concepts such as agencies, operators, authorities, publishers, owners, or
service providers.

These terms describe roles and responsibilities, not necessarily different
semantic entity classes.

SCRA therefore models Organisation as the role-neutral organisational entity
and represents specific responsibilities through relationships or properties.

---

## Characteristics

An Organisation:

- may operate one or more ServiceJourneys;
- may operate, manage, or own one or more PhysicalVehicles;
- may act as a transport authority;
- may publish transport data;
- may maintain planning or service-calendar information;
- may perform several of these roles simultaneously; and
- shall not be inferred to perform every possible role merely because one
  organisational source record exists.

---

## Relationships

### operator

Where an Organisation performs the operator role for a ServiceJourney or
PhysicalVehicle, that role is represented by the normative `operator`
relationship from the operated entity to the Organisation.

The role is contextual. The same Organisation may be the operator in one
relationship while acting as an authority, publisher, or owner in another
context.

---

## Typical Properties

Typical semantic properties may include:

- identifier;
- name;
- short name;
- legal name;
- contact information;
- website;
- timezone;
- source identifier; and
- provenance.

These property names are informative unless separately declared normative in
the SCRA Vocabulary.

---

## Conformance Requirements

A conforming Organisation representation shall:

- identify an organisational entity rather than an organisational role;
- preserve identifier scope where source identifiers are not globally unique;
- avoid inferring operator, authority, publisher, or ownership roles unless the
  source data or applicable profile supports that interpretation; and
- permit the same Organisation to participate in multiple role relationships.

---

## Mapping Guidance

### GTFS Static

A GTFS `agency.txt` record SHOULD map to an Organisation instance.

The mapping SHALL preserve the fact that a GTFS agency may represent a service
operator, transport authority, data publisher, owner, manager, or another
organisational role depending on the source dataset.

A mapping SHALL NOT infer that one `agency.txt` record is simultaneously all of
those roles.

`agency_id` is a source identifier for the Organisation and SHALL be interpreted
within the applicable source or authority namespace.

Where `routes.txt.agency_id` identifies the organisation responsible for a
route, the mapping profile may use that evidence to establish an operator or
authority relationship only when the source semantics justify the specific
role.

### GTFS-Realtime

GTFS-Realtime does not generally publish a first-class organisation record.

Where the source feed or deployment context identifies an operator,
organisation, or authority, that identity may be linked to an existing
Organisation instance, but the realtime feed SHALL NOT silently create operator
semantics that are absent from the source context.

---

## See Also

- ServiceJourney
- PhysicalVehicle
- ServiceCalendar
- DatedVehicleJourney
- MonitoredVehicleJourney

---

<!-- SCRA-PHASE4-ORGANISATION-MODEL -->

© SCRA Project

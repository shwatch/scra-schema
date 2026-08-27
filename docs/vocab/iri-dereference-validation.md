# Vocabulary IRI Dereference Validation

## Status

Validated

## Purpose

This document records the publication-path validation for the Phase 2 READY-11
SCRA vocabulary terms.

For each normative SCRA vocabulary IRI, dereferencing the canonical path SHALL
lead to the corresponding human-readable normative vocabulary declaration.

On case-insensitive development filesystems, class and relationship terms such
as `ServiceJourney` / `serviceJourney` cannot safely be represented by
case-only Markdown filenames. SCRA therefore uses explicit declaration
filenames where required and lightweight static redirect pages at the canonical
IRI paths.

## Validation Matrix

| Term | Canonical IRI | Publication mechanism | Result |
|---|---|---|---|
| `MonitoredVehicleJourney` | `https://scra-schema.org/vocab/MonitoredVehicleJourney` | static redirect → `MonitoredVehicleJourney-class/` | Pass |
| `OperatingDay` | `https://scra-schema.org/vocab/OperatingDay` | direct Markdown page `OperatingDay.md` | Pass |
| `PhysicalVehicle` | `https://scra-schema.org/vocab/PhysicalVehicle` | direct Markdown page `PhysicalVehicle.md` | Pass |
| `assignedVehicle` | `https://scra-schema.org/vocab/assignedVehicle` | direct Markdown page `assignedVehicle.md` | Pass |
| `datedVehicleJourney` | `https://scra-schema.org/vocab/datedVehicleJourney` | static redirect → `datedVehicleJourney-relationship/` | Pass |
| `dayType` | `https://scra-schema.org/vocab/dayType` | static redirect → `dayType-relationship/` | Pass |
| `journeyPattern` | `https://scra-schema.org/vocab/journeyPattern` | static redirect → `journeyPattern-relationship/` | Pass |
| `operatingDay` | `https://scra-schema.org/vocab/operatingDay` | static redirect → `operatingDay-relationship/` | Pass |
| `route` | `https://scra-schema.org/vocab/route` | direct Markdown page `route.md` | Pass |
| `serviceJourney` | `https://scra-schema.org/vocab/serviceJourney` | static redirect → `serviceJourney-relationship/` | Pass |
| `stop` | `https://scra-schema.org/vocab/stop` | direct Markdown page `stop.md` | Pass |

## Conformance Note

The redirect mechanism changes only the human-readable publication path.
It does not change the canonical vocabulary IRI, JSON-LD compact-term mapping,
or semantic meaning of the term.

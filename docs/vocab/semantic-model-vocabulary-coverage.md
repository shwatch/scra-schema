# Semantic Model to Vocabulary Coverage

## Status

Validated Phase 2 coverage matrix

## Purpose

This matrix makes the publication chain explicit:

**Semantic Model → NGSI-LD semantic role → Vocabulary IRI → JSON-LD compact term**

Only terms classified READY by the Phase 2 readiness diagnostic are promoted
here. REVIEW terms remain non-normative until their semantic questions are
resolved.

## READY Terms

| Semantic term | Kind | Semantic / mapping basis | Normative Vocabulary IRI | Context term | Status |
|---|---|---|---|---|---|
| `MonitoredVehicleJourney` | Entity Type | [`docs/models/MonitoredVehicleJourney.md`](../models/MonitoredVehicleJourney.md) | [`https://scra-schema.org/vocab/MonitoredVehicleJourney`](MonitoredVehicleJourney-class.md) | `MonitoredVehicleJourney` | Normative |
| `OperatingDay` | Entity Type | [`docs/models/OperatingDay.md`](../models/OperatingDay.md) | [`https://scra-schema.org/vocab/OperatingDay`](OperatingDay.md) | `OperatingDay` | Normative |
| `PhysicalVehicle` | Entity Type | [`docs/models/PhysicalVehicle.md`](../models/PhysicalVehicle.md) | [`https://scra-schema.org/vocab/PhysicalVehicle`](PhysicalVehicle.md) | `PhysicalVehicle` | Normative |
| `assignedVehicle` | Relationship | DatedVehicleJourney → PhysicalVehicle | [`https://scra-schema.org/vocab/assignedVehicle`](assignedVehicle.md) | `assignedVehicle` | Normative |
| `datedVehicleJourney` | Relationship | MonitoredVehicleJourney → DatedVehicleJourney | [`https://scra-schema.org/vocab/datedVehicleJourney`](datedVehicleJourney-relationship.md) | `datedVehicleJourney` | Normative |
| `dayType` | Relationship | OperatingDay → DayType | [`https://scra-schema.org/vocab/dayType`](dayType-relationship.md) | `dayType` | Normative |
| `journeyPattern` | Relationship | ServiceJourney / PointInJourneyPattern → JourneyPattern | [`https://scra-schema.org/vocab/journeyPattern`](journeyPattern-relationship.md) | `journeyPattern` | Normative |
| `operatingDay` | Relationship | DatedVehicleJourney → OperatingDay | [`https://scra-schema.org/vocab/operatingDay`](operatingDay-relationship.md) | `operatingDay` | Normative |
| `route` | Relationship | ServiceJourney / JourneyPattern → PublicTransportRoute | [`https://scra-schema.org/vocab/route`](route.md) | `route` | Normative |
| `serviceJourney` | Relationship | DatedVehicleJourney → ServiceJourney | [`https://scra-schema.org/vocab/serviceJourney`](serviceJourney-relationship.md) | `serviceJourney` | Normative |
| `stop` | Relationship | PassingEvent / PointInJourneyPattern → PublicTransportStop | [`https://scra-schema.org/vocab/stop`](stop.md) | `stop` | Normative |

## Deferred REVIEW Relationships

| Term | Kind | Reason | Vocabulary IRI | Context term | Status |
|---|---|---|---|---|---|
| `currentPoint` | Relationship | NGSI-LD candidate / semantic review required | — | — | Review |
| `currentStop` | Relationship | NGSI-LD candidate / semantic review required | — | — | Review |
| `operatedByVehicle` | Relationship | NGSI-LD candidate / semantic review required | — | — | Review |
| `operator` | Relationship | NGSI-LD candidate / semantic review required | — | — | Review |
| `serviceCalendar` | Relationship | NGSI-LD candidate / semantic review required | — | — | Review |

## Conformance Note

A row marked `Normative` means that the listed SCRA vocabulary IRI is the
canonical SCRA IRI for that semantic term. The semantic model remains the
authority for class meaning and constraints; the vocabulary declaration
provides the stable IRI, and the Mobility JSON-LD Context provides compact-term
expansion.

<!-- SCRA-PHASE3-READY4-COVERAGE -->

## Phase 3 READY Relationships

| Semantic term | Kind | Domain | Range | Status | Semantic / mapping basis |
|---|---|---|---|---|---|
| `currentPoint` | Relationship | `MonitoredVehicleJourney` | `PointInJourneyPattern` | Normative | Semantic Model + NGSI-LD mapping |
| `currentStop` | Relationship | `MonitoredVehicleJourney` | `PublicTransportStop` | Normative | Semantic Model + NGSI-LD mapping |
| `operatedByVehicle` | Relationship | `MonitoredVehicleJourney` | `PhysicalVehicle` | Normative | Semantic Model + NGSI-LD mapping |
| `serviceCalendar` | Relationship | `ServiceJourney / OperatingDay` | `ServiceCalendar` | Normative | NGSI-LD range cleanup; DayType separated to `dayType` |

`operator` remains REVIEW / design-decision-required until one canonical
operator/organisation range class is selected.

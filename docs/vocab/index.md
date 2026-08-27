# SCRA Mobility Vocabulary

## Status

Draft

---

This section publishes the compact vocabulary terms used by the initial SCRA
public transport profile and the Mobility MCP proof of concept.

Each term has an authoritative SCRA vocabulary IRI under:

```text
https://scra-schema.org/vocab/
```

The machine-readable JSON-LD context is published at:

```text
https://scra-schema.org/contexts/mobility.jsonld
```

## Initial Passing-Event Vocabulary

### Entity Type

- [PassingEvent](PassingEvent.md)

### Relationships

- [monitoredVehicleJourney](monitoredVehicleJourney.md)
- [stop](stop.md)

### Properties

- [eventType](eventType.md)
- [scheduledTime](scheduledTime.md)
- [predictedTime](predictedTime.md)
- [observedTime](observedTime.md)
- [predictionConfidence](predictionConfidence.md)
- [observationTimestamp](observationTimestamp.md)
- [delay](delay.md)
- [minutesUntilArrival](minutesUntilArrival.md)
- [referenceTime](referenceTime.md)

## Normative Core Public-Transport Classes

The following semantic classes have stable, normative SCRA vocabulary IRIs.
Their semantic definitions remain in the corresponding semantic-model
documents.

### Entity Types

- [ServiceJourney](ServiceJourney.md)
- [JourneyPattern](JourneyPattern.md)
- [PointInJourneyPattern](PointInJourneyPattern.md)
- [DatedVehicleJourney](DatedVehicleJourney.md)
- [ServiceCalendar](ServiceCalendar.md)
- [DayType](DayType.md)

## Vocabulary Policy

SCRA reuses an existing international vocabulary term when it has compatible
meaning. An SCRA term is published when no suitable term exists, when existing
terms conflict, or when a harmonized term is required for reliable
interoperability.

Each vocabulary term declares its own publication status. A term marked
`Normative` defines requirements for conforming implementations. A term marked
`Draft` remains subject to change and shall not be treated as a stable
interoperability contract.

<!-- BEGIN READY11 NORMATIVE VOCABULARY -->
## Normative Mobility Vocabulary — Phase 2 READY Set

These terms were promoted only after Semantic Model and NGSI-LD mapping
evidence classified them as ready for stable vocabulary publication.

### Entity Types

- [MonitoredVehicleJourney](MonitoredVehicleJourney-class.md)
- [OperatingDay](OperatingDay.md)
- [PhysicalVehicle](PhysicalVehicle.md)

### Relationships

- [`assignedVehicle`](assignedVehicle.md)
- [`datedVehicleJourney`](datedVehicleJourney-relationship.md)
- [`dayType`](dayType-relationship.md)
- [`journeyPattern`](journeyPattern-relationship.md)
- [`operatingDay`](operatingDay-relationship.md)
- [`route`](route.md)
- [`serviceJourney`](serviceJourney-relationship.md)
- [`stop`](stop.md)

<!-- END READY11 NORMATIVE VOCABULARY -->

<!-- SCRA-PHASE3-READY4 -->

## Normative Mobility Vocabulary — Phase 3 READY Set

The following relationships were promoted after cross-layer review of the
Semantic Models and NGSI-LD mapping.

### Relationships

- [currentPoint](currentPoint.md)
- [currentStop](currentStop.md)
- [operatedByVehicle](operatedByVehicle.md)
- [serviceCalendar](serviceCalendar-relationship.md)

<!-- SCRA-PHASE4-OPERATOR -->

## Normative Mobility Vocabulary — Phase 4 Organisation and Operator

### Entity Types

- [Organisation](Organisation.md)

### Relationships

- [operator](operator.md)

The `operator` relationship has normative domain `PhysicalVehicle`,
`ServiceJourney` and normative range `Organisation`.

`DatedVehicleJourney` is intentionally excluded from the normative operator
domain until its semantic model contains a dedicated operator definition.

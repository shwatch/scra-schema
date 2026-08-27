# Mobility Context Validation

## Status

Validated

## Scope

This report records validation of the NGSI-LD JSON-LD context terms used by
the normative SCRA `PassingEvent` vocabulary.

The validation does not modify `mobility.jsonld`.

## Context

- File: `docs/contexts/mobility.jsonld`
- JSON-LD version: `1.1`
- SCRA vocabulary prefix: `https://scra-schema.org/vocab/`

## Validation Result

All normative `PassingEvent` terms are present and map to their expected SCRA
vocabulary IRIs.

| Compact term | Context mapping | Expanded IRI | Result |
|---|---|---|---|
| `ServiceJourney` | `scra:ServiceJourney` | `https://scra-schema.org/vocab/ServiceJourney` | Pass |
| `JourneyPattern` | `scra:JourneyPattern` | `https://scra-schema.org/vocab/JourneyPattern` | Pass |
| `PointInJourneyPattern` | `scra:PointInJourneyPattern` | `https://scra-schema.org/vocab/PointInJourneyPattern` | Pass |
| `DatedVehicleJourney` | `scra:DatedVehicleJourney` | `https://scra-schema.org/vocab/DatedVehicleJourney` | Pass |
| `ServiceCalendar` | `scra:ServiceCalendar` | `https://scra-schema.org/vocab/ServiceCalendar` | Pass |
| `DayType` | `scra:DayType` | `https://scra-schema.org/vocab/DayType` | Pass |
| `PassingEvent` | `scra:PassingEvent` | `https://scra-schema.org/vocab/PassingEvent` | Pass |
| `monitoredVehicleJourney` | `scra:monitoredVehicleJourney` | `https://scra-schema.org/vocab/monitoredVehicleJourney` | Pass |
| `stop` | `scra:stop` | `https://scra-schema.org/vocab/stop` | Pass |
| `eventType` | `scra:eventType` | `https://scra-schema.org/vocab/eventType` | Pass |
| `scheduledTime` | `scra:scheduledTime` | `https://scra-schema.org/vocab/scheduledTime` | Pass |
| `predictedTime` | `scra:predictedTime` | `https://scra-schema.org/vocab/predictedTime` | Pass |
| `observedTime` | `scra:observedTime` | `https://scra-schema.org/vocab/observedTime` | Pass |
| `predictionConfidence` | `scra:predictionConfidence` | `https://scra-schema.org/vocab/predictionConfidence` | Pass |
| `observationTimestamp` | `scra:observationTimestamp` | `https://scra-schema.org/vocab/observationTimestamp` | Pass |
| `delay` | `scra:delay` | `https://scra-schema.org/vocab/delay` | Pass |
| `minutesUntilArrival` | `scra:minutesUntilArrival` | `https://scra-schema.org/vocab/minutesUntilArrival` | Pass |
| `referenceTime` | `scra:referenceTime` | `https://scra-schema.org/vocab/referenceTime` | Pass |

## Conclusion

No missing or mismatched normative `PassingEvent` vocabulary terms were found.
No change to `mobility.jsonld` is required by this validation.

<!-- BEGIN READY11 CONTEXT VALIDATION -->
## Phase 2 READY-11 Vocabulary Expansion Validation

The following additional Entity Type and Relationship terms are normative and
are required to expand through the SCRA Mobility Context.

| Compact term | Context mapping | Expanded IRI | Result |
|---|---|---|---|
| `MonitoredVehicleJourney` | `scra:MonitoredVehicleJourney` | `https://scra-schema.org/vocab/MonitoredVehicleJourney` | Pass |
| `OperatingDay` | `scra:OperatingDay` | `https://scra-schema.org/vocab/OperatingDay` | Pass |
| `PhysicalVehicle` | `scra:PhysicalVehicle` | `https://scra-schema.org/vocab/PhysicalVehicle` | Pass |
| `assignedVehicle` | `scra:assignedVehicle` | `https://scra-schema.org/vocab/assignedVehicle` | Pass |
| `datedVehicleJourney` | `scra:datedVehicleJourney` | `https://scra-schema.org/vocab/datedVehicleJourney` | Pass |
| `dayType` | `scra:dayType` | `https://scra-schema.org/vocab/dayType` | Pass |
| `journeyPattern` | `scra:journeyPattern` | `https://scra-schema.org/vocab/journeyPattern` | Pass |
| `operatingDay` | `scra:operatingDay` | `https://scra-schema.org/vocab/operatingDay` | Pass |
| `route` | `scra:route` | `https://scra-schema.org/vocab/route` | Pass |
| `serviceJourney` | `scra:serviceJourney` | `https://scra-schema.org/vocab/serviceJourney` | Pass |
| `stop` | `scra:stop` | `https://scra-schema.org/vocab/stop` | Pass |
<!-- END READY11 CONTEXT VALIDATION -->

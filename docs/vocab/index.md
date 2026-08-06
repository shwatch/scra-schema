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

## Vocabulary Policy

SCRA reuses an existing international vocabulary term when it has compatible
meaning. An SCRA term is published when no suitable term exists, when existing
terms conflict, or when a harmonized term is required for reliable
interoperability.

Each vocabulary term declares its own publication status. A term marked
`Normative` defines requirements for conforming implementations. A term marked
`Draft` remains subject to change and shall not be treated as a stable
interoperability contract.

# ADR-0003: Represent Scheduled Public Transport Operations as Service Journeys

## Status

Accepted

## Context

SCRA and Mobility MCP require a source-independent semantic representation
of scheduled public transport operations.

GTFS represents scheduled operations primarily through `trips.txt`.

GTFS defines a trip as a sequence of two or more stops that occurs during
a specific time period. A GTFS trip references a route through `route_id`
and a set of service dates through `service_id`.

Although the term `Trip` is established in GTFS, it is potentially ambiguous
in a broader MaaS semantic model because "trip" may also describe a passenger's
journey.

SCRA therefore requires a semantic concept that clearly distinguishes the
operation of a public transport service from the journey undertaken by a
passenger.

Transmodel provides such a distinction through its VEHICLE JOURNEY and
SERVICE JOURNEY concepts.

The Transmodel / NeTEx GTFS harmonisation material identifies a GTFS
`trips.txt` record as corresponding generally to a VEHICLE JOURNEY.

More specifically:

- an individual GTFS Trip operating at a specific time corresponds to a
  SERVICE JOURNEY;
- a GTFS Trip operated according to a frequency corresponds to a
  TEMPLATE SERVICE JOURNEY, which represents multiple SERVICE JOURNEY
  instances.

This distinction SHALL be preserved by the SCRA semantic model.

## Decision

SCRA and Mobility MCP SHALL NOT use `GtfsTrip` as the canonical semantic
entity type merely because source data originates from GTFS.

For a scheduled passenger-carrying public transport operation at a specific
time, SCRA SHALL use the semantic concept `ServiceJourney`.

`ServiceJourney` is based on the Transmodel SERVICE JOURNEY concept.

The broader Transmodel VEHICLE JOURNEY concept SHALL remain relevant when
mapping source data whose semantics are not limited to passenger-carrying
service journeys.

Frequency-based GTFS services SHALL NOT automatically be treated as a
single ordinary `ServiceJourney`.

Where the distinction is relevant, the mapping layer SHALL preserve the
difference between a template or frequency definition and the individual
service journeys generated from it.

## Terminology

SCRA SHALL distinguish between:

- a passenger journey or passenger trip; and
- a public transport service journey.

`ServiceJourney` refers to the scheduled movement of a passenger-carrying
public transport service.

It does not represent the complete journey undertaken by a passenger.

This distinction is important for MaaS applications where a passenger
journey may use multiple ServiceJourneys and may also contain walking,
transfer, cycling, taxi, or other journey legs.

Conceptually:

    PassengerJourney
           |
           +-- walking / access leg
           |
           +-- uses --> ServiceJourney
           |
           +-- transfer
           |
           +-- uses --> ServiceJourney
           |
           +-- walking / egress leg

The precise semantic model for passenger journeys is outside the scope of
this ADR.

## Relationship to PublicTransportRoute

A `ServiceJourney` SHALL be capable of referring to the semantic route on
which the service operates.

For GTFS input:

    trips.route_id
          |
          | semantic mapping
          v
    ServiceJourney
          |
          +-- route --> PublicTransportRoute

The association SHOULD be represented as an NGSI-LD Relationship rather
than as a source-specific foreign-key value.

The precise relationship vocabulary and IRI SHALL be verified separately
before being made normative.

## Service Dates

GTFS `service_id` identifies a set of dates on which service is available.

This source-specific identifier SHALL NOT by itself define the canonical
semantic representation of operating-day semantics.

The mapping layer SHALL interpret GTFS calendar information and map it to
an appropriate semantic representation of service calendars, day types,
operating periods, or operating days.

The exact SCRA model for service calendars and operating days is outside
the scope of this ADR and SHALL be decided separately.

## Initial Mapping Candidates

The following mappings describe the current semantic direction:

| GTFS field | Candidate semantic representation |
| --- | --- |
| `trip_id` | Source identifier used in construction or correlation of a ServiceJourney identity |
| `route_id` | NGSI-LD Relationship to `PublicTransportRoute` |
| `service_id` | Relationship to a future service-calendar / operating-day semantic model |
| `trip_headsign` | Passenger-facing destination / headsign property |
| `trip_short_name` | Passenger-facing journey identifier |
| `direction_id` | Semantic direction property |
| `shape_id` | Relationship to journey pattern / geometry semantics |
| `block_id` | Operational block relationship where required |

These mappings are not made normative by this ADR.

Each SHALL be verified against existing vocabulary definitions before being
adopted into the SCRA vocabulary or reference implementation.

## GTFS-Realtime and Journey Instances

GTFS-Realtime introduces additional semantics through `TripDescriptor`.

A TripDescriptor may use information including:

- `trip_id`
- `route_id`
- `direction_id`
- `start_time`
- `start_date`
- `schedule_relationship`

For ordinary non-frequency-based scheduled trips, `trip_id` can normally
identify the corresponding GTFS trip.

For frequency-based services, GTFS-Realtime requires additional information
such as `start_time` and `start_date` to identify a particular trip instance.

GTFS-Realtime can also describe relationships to the static schedule such
as newly created, replacement, duplicated, or otherwise modified trips.

SCRA SHALL therefore distinguish conceptually between:

1. the reusable or scheduled service definition; and
2. a service operation associated with a particular operating day and,
   where required, a particular start time.

Transmodel provides the DATED VEHICLE JOURNEY concept for a vehicle journey
planned for one specific OPERATING DAY, including short-term modifications.

This provides an established semantic basis for modelling dated operational
journeys.

However, this ADR does not yet establish the final SCRA entity type or IRI
for the real-time operational journey instance.

That decision SHALL be made separately after the relationship between
GTFS-Realtime TripDescriptor semantics, ServiceJourney, DatedVehicleJourney,
VehiclePosition, and TripUpdate has been fully verified.

## Planned and Real-Time Layers

The semantic architecture SHALL permit the following conceptual separation:

    PublicTransportRoute
             ^
             |
        ServiceJourney
             |
             | scheduled / dated relationship
             v
    Dated operational journey
             ^
             |
             +-- VehiclePosition
             |
             +-- TripUpdate
             |
             +-- Vehicle

This separation prevents real-time observations from redefining the
underlying scheduled service.

It also allows multiple source standards to provide planned or real-time
information about the same semantic transport operation.

## Transmodel and NeTEx

Transmodel is an implementation-independent conceptual reference data model
for public transport.

NeTEx provides an implementation and exchange representation based on
Transmodel concepts and uses XML schemas for data exchange.

The existence of a Transmodel concept therefore does not by itself imply
the existence of a directly reusable official RDF or JSON-LD vocabulary IRI.

SCRA SHOULD use established Transmodel semantics wherever they adequately
represent the required public transport concept.

Where an appropriate established concept exists but no suitable stable
official RDF or JSON-LD vocabulary IRI is available for direct use in an
NGSI-LD `@context`, SCRA MAY provide a stable SCRA vocabulary IRI.

For example, subject to final vocabulary verification:

    https://scra-schema.org/vocab/ServiceJourney

Such an IRI would not define a new transport concept.

Its definition SHALL identify Transmodel SERVICE JOURNEY as the semantic
basis of the SCRA term.

## Vocabulary Reuse Policy

This ADR follows the SCRA vocabulary reuse policy established by previous
decisions:

1. NGSI-LD core vocabulary
2. suitable existing Smart Data Models vocabulary
3. suitable concepts from established domain standards such as
   Transmodel / NeTEx
4. other suitable established vocabularies
5. SCRA-defined vocabulary only where an adequate directly reusable
   vocabulary term is not available

A SCRA IRI MAY therefore be used to make an established external semantic
concept directly usable in JSON-LD / NGSI-LD without claiming invention of
the underlying domain concept.

## Rationale

`GtfsTrip` is appropriate when representing GTFS data while retaining the
GTFS source model.

SCRA has a broader objective.

Equivalent public transport operations originating from GTFS, NeTEx,
provider APIs, municipal systems, and other mobility standards should be
capable of converging on a common semantic representation.

Conceptually:

    GTFS Trip -----------------+
                               |
    NeTEx ServiceJourney ------+--> ServiceJourney
                               |
    Provider timetable API ----+
                               |
    Other mobility standard ---+

Using `ServiceJourney` also avoids ambiguity between a vehicle/service
operation and a passenger's overall journey.

This distinction becomes particularly important in MaaS, where one
passenger journey may contain multiple public transport ServiceJourneys.

## Consequences

- `GtfsTrip` SHALL NOT be the canonical SCRA semantic entity type merely
  because the source is GTFS.
- Scheduled passenger-carrying operations at specific times SHOULD be
  represented as `ServiceJourney`.
- The semantic basis of `ServiceJourney` SHALL be the Transmodel
  SERVICE JOURNEY concept.
- GTFS frequency-based trips SHALL preserve their template/frequency
  semantics where relevant.
- `ServiceJourney` and passenger journey concepts SHALL remain distinct.
- Route references SHOULD become NGSI-LD Relationships to
  `PublicTransportRoute`.
- Service-calendar semantics SHALL be modelled separately from the GTFS
  `service_id` foreign key.
- GTFS-Realtime journey-instance semantics SHALL be represented separately
  from the underlying scheduled ServiceJourney where necessary.
- DATED VEHICLE JOURNEY provides an established candidate semantic basis
  for dated operational journeys.
- The final SCRA representation of a dated or real-time journey instance
  SHALL be decided after further analysis.
- Existing semantic concepts SHALL be reused before introducing new SCRA
  concepts.
- SCRA MAY provide a JSON-LD vocabulary IRI for an established Transmodel
  concept where no suitable directly reusable official IRI is available.

## Evidence

### GTFS Schedule

The GTFS Schedule Reference defines `trips.txt`.

A GTFS trip is a sequence of two or more stops occurring during a specific
time period.

`route_id` identifies the route associated with the trip.

`service_id` identifies the set of dates on which the service is available.

GTFS also defines passenger-facing and operational attributes including
`trip_headsign`, `trip_short_name`, `direction_id`, `block_id`, and
`shape_id`.

### Transmodel / NeTEx GTFS Harmonisation

The Transmodel standards harmonisation material identifies a GTFS
`trips.txt` record as corresponding generally to a VEHICLE JOURNEY.

It further distinguishes:

- an individual GTFS Trip running at a specific time as a SERVICE JOURNEY;
- a frequency-based GTFS Trip as a TEMPLATE SERVICE JOURNEY.

This provides direct evidence for using `ServiceJourney` as the semantic
basis for ordinary scheduled GTFS passenger trips while preserving the
frequency/template distinction.

### GTFS-Realtime

GTFS-Realtime `TripDescriptor` identifies or describes a trip instance
using fields including `trip_id`, `route_id`, `start_time`, `start_date`,
and `schedule_relationship`.

Frequency-based trip instances require additional identifying information.

GTFS-Realtime also supports operational relationships to the static
schedule, demonstrating that the real-time journey instance cannot always
be treated as identical to the static GTFS trip definition.

### Transmodel Dated Vehicle Journey

Transmodel defines DATED VEHICLE JOURNEY as a vehicle journey planned for
one specific OPERATING DAY.

The operations model allows short-term modifications to dated journeys.

This provides an established semantic basis for later modelling of
GTFS-Realtime operational journey instances.

### Transmodel and NeTEx Implementation

Transmodel is implementation-independent.

NeTEx provides a standardized XML exchange representation based on
Transmodel concepts.

SCRA therefore treats Transmodel primarily as a semantic reference model
and may provide JSON-LD / NGSI-LD vocabulary IRIs where required.

## References

- GTFS Schedule Reference:
  https://gtfs.org/documentation/schedule/reference/

- GTFS-Realtime Reference:
  https://gtfs.org/documentation/realtime/reference/

- Transmodel / NeTEx GTFS Standards Harmonisation:
  https://transmodel-cen.eu/wp-content/uploads/2024/07/StandardsHarmonisation-2019-njsk-v1.0-1.pdf

- Transmodel Operations Monitoring and Control:
  https://transmodel-cen.eu/index.php/oper/

- Transmodel — Dated Vehicle Journey:
  https://www.transmodel-cen.eu/model/EARoot/EA3/EA3/EA937.htm

- NeTEx:
  https://transmodel-cen.eu/index.php/netex/

- Transmodel — Standards for Implementation:
  https://transmodel-cen.eu/index.php/standards-for-implementation/
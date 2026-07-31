# ADR-0005: Represent Vehicles as Physical Transport Entities

## Status

Accepted

## Context

SCRA and Mobility MCP require a source-independent semantic representation
of vehicles used in public transport operations.

ADR-0004 establishes `MonitoredVehicleJourney` as the semantic representation
of the monitored state of a particular public transport operation.

A monitored journey may be performed by a particular physical vehicle.
The semantic model therefore requires a concept representing that physical
vehicle independently from the journey that it performs.

GTFS-Realtime provides vehicle information primarily through
`VehicleDescriptor`, which may occur in `VehiclePosition` and `TripUpdate`.

The GTFS-Realtime specification defines `VehicleDescriptor` as identification
information for the vehicle performing the trip.

Its `id` field is an internal system identifier that should be unique per
vehicle and may be used to track the vehicle as it proceeds through the
transport system.

Other fields include:

- `label`, a passenger-visible identifier;
- `license_plate`, the vehicle registration plate; and
- `wheelchair_accessible`, an operational accessibility indication that may
  override the corresponding value in static GTFS.

These fields must not be interpreted merely by reproducing the nested
GTFS-Realtime message structure.

SCRA instead requires a semantic representation of the underlying transport
concepts.

## Evidence from Transmodel

Transmodel provides an explicit distinction between a physical vehicle and
the type of vehicle to which it belongs.

The Transmodel common concepts documentation states that the `VEHICLE` entity
describes physical public transport vehicles available for short-term
planning of operations and daily assignment.

Each `VEHICLE` is classified as a particular `VEHICLE TYPE`.

The `VEHICLE TYPE` concept describes characteristics shared by vehicles,
including characteristics relevant to transport mode, passenger capacity,
manoeuvring requirements, and available facilities.

This distinction provides an appropriate semantic basis for SCRA:

- `Vehicle` represents an individual physical transport vehicle;
- `VehicleType` represents a classification shared by vehicles; and
- vehicle model information may be represented separately where required.

This distinction also prevents operational observations of a particular
vehicle from being confused with properties of a vehicle class or model.

## Smart Data Models Assessment

Smart Data Models provides a `Vehicle` entity in
`dataModel.Transportation`.

However, its published definition currently describes the entity as modelling
a particular vehicle model and properties common to multiple vehicle
instances.

At the same time, the same `Vehicle` model contains attributes that naturally
describe an individual physical vehicle or its current state, including
attributes such as:

- `fleetVehicleId`;
- `license_plate`;
- `location`;
- `bearing`;
- `speed`;
- `mileageFromOdometer`; and
- `refVehicleModel`.

Smart Data Models also provides a separate `VehicleModel` entity.

There is therefore a semantic ambiguity between the published definition of
the Smart Data Models `Vehicle` entity and several of the attributes contained
within that model.

SCRA SHALL NOT resolve this ambiguity by automatically adopting the complete
Smart Data Models `Vehicle` entity as its canonical vehicle definition.

This does not prevent reuse of Smart Data Models vocabulary.

Individual attributes or relationships from Smart Data Models SHOULD be
reused where their semantics clearly match the SCRA concept and where such
reuse does not introduce source-specific or model-specific ambiguity.

This decision follows the general SCRA principle:

> Reuse existing semantic vocabulary where the meaning is compatible, but do
> not inherit an existing model where doing so would weaken or distort the
> intended canonical semantics.

## Decision

SCRA SHALL define `Vehicle` as an individual physical transport vehicle.

A `Vehicle` represents the persistent identity of the physical vehicle and is
independent from any particular scheduled or monitored journey.

A vehicle may participate in many journeys over time.

The relationship between a monitored operation and the physical vehicle
performing that operation SHALL be represented semantically, conceptually as:

`MonitoredVehicleJourney --operatedByVehicle--> Vehicle`

The exact vocabulary IRI for this relationship may be defined separately.

The canonical meaning of `Vehicle` SHALL be aligned primarily with the
Transmodel concept of `VEHICLE`.

SCRA MAY reuse compatible Smart Data Models attributes and relationships
where their semantics are sufficiently clear.

The Smart Data Models `Vehicle` entity SHALL NOT, however, be adopted wholesale
as the normative semantic definition of SCRA `Vehicle` while the distinction
between vehicle instance and vehicle model remains ambiguous.

## Vehicle Identity

A `Vehicle` SHALL have a persistent identity representing the physical vehicle
rather than the GTFS-Realtime message in which the vehicle was observed.

Source-specific identifiers may be mapped to this identity.

For GTFS-Realtime, `VehicleDescriptor.id` is the preferred source identifier
when it is available and stable within the source system.

The source identifier SHALL be interpreted within an appropriate identity
namespace so that identifiers from independent transport providers or source
systems do not collide.

`VehicleDescriptor.label` SHALL NOT normally be used as the canonical vehicle
identity because GTFS-Realtime defines it as a passenger-visible label rather
than as the internal tracking identifier.

`VehicleDescriptor.license_plate` MAY be represented as an attribute of the
vehicle when provided, but SHALL NOT be assumed to be the canonical semantic
identifier.

## Vehicle State

The persistent identity of a vehicle SHALL be distinguished from observations
of its current operational state.

GTFS-Realtime `VehiclePosition` may provide information such as:

- geographic position;
- bearing;
- speed;
- odometer information;
- current stop;
- current stop sequence;
- vehicle stop status;
- occupancy information; and
- observation timestamp.

These values describe the observed or reported state associated with a vehicle
and its current operation.

They do not define the identity of the physical vehicle.

Where appropriate, SCRA SHOULD reuse established properties such as
`location`, `bearing`, and `speed` when their semantics are compatible.

Temporal information SHALL preserve the time at which the state was observed
or reported.

The semantic model SHALL remain capable of distinguishing the current state
of a `Vehicle` from the current state of the corresponding
`MonitoredVehicleJourney`.

For example, geographic position may describe the physical vehicle, while
progress through a service journey describes the monitored operation.

## Vehicle Type and Vehicle Model

SCRA SHALL preserve the semantic distinction between:

- an individual `Vehicle`;
- a `VehicleType`; and
- a `VehicleModel`, where model information is available.

A `VehicleType` describes characteristics shared by a class of vehicles.

A `VehicleModel` may describe manufacturer or model-specific characteristics.

Neither SHALL replace the identity of the individual physical `Vehicle`.

Where compatible external vocabulary exists, SCRA SHOULD reuse it rather than
creating unnecessary duplicate terms.

## GTFS-Realtime Mapping

GTFS-Realtime structures SHALL be mapped semantically rather than copied
directly.

Conceptually:

    GTFS-Realtime VehicleDescriptor
                  |
                  v
               Vehicle
        physical vehicle identity

    GTFS-Realtime VehiclePosition
                  |
                  +---- updates observed vehicle state
                  |
                  +---- contributes to monitored journey state
                  v
        MonitoredVehicleJourney

The vehicle participating in an operation is represented by a relationship:

    MonitoredVehicleJourney
              |
              | operatedByVehicle
              v
            Vehicle

This allows vehicle identity to remain stable while location, speed, bearing,
journey assignment, and other operational information change over time.

## Accessibility Information

GTFS-Realtime places `wheelchair_accessible` within `VehicleDescriptor`.

However, the GTFS-Realtime specification defines this value in terms of
whether the particular trip is wheelchair accessible and allows it to
override the corresponding static GTFS value.

SCRA SHALL therefore NOT automatically interpret
`VehicleDescriptor.wheelchair_accessible` as a permanent intrinsic property
of the physical `Vehicle`.

The mapping SHALL preserve its operational meaning.

Its final canonical placement may be associated with the relevant journey,
vehicle assignment, accessibility state, or another appropriate semantic
concept defined by the SCRA schema.

This shall be resolved separately from the definition of physical vehicle
identity.

## Consequences

This decision separates four concepts that source formats may otherwise mix:

1. the physical vehicle;
2. the type or model of that vehicle;
3. the monitored public transport operation performed by the vehicle; and
4. observations describing the current operational state.

As a result:

- vehicle identity can remain stable across multiple journeys;
- GTFS-Realtime structures do not leak into the canonical semantic model;
- vehicle information from non-GTFS systems can use the same semantic entity;
- Transmodel concepts can be mapped without collapsing vehicle and journey;
- compatible Smart Data Models vocabulary can still be reused;
- ambiguous external model definitions do not become normative SCRA semantics;
- real-time vehicle observations can evolve independently from vehicle
  identity; and
- MaaS and multimodal systems can refer consistently to physical transport
  resources.

## Rejected Alternative: Adopt Smart Data Models Vehicle Without Qualification

One alternative was to adopt the complete Smart Data Models
`dataModel.Transportation/Vehicle` entity as the canonical SCRA vehicle model.

This would maximize direct reuse of an existing NGSI-LD-oriented model.

It was rejected as the normative semantic definition because the published
definition describes a vehicle model shared by multiple vehicle instances,
while several attributes describe an individual physical vehicle or its
dynamic state.

Adopting the model wholesale would therefore introduce ambiguity into a
foundational SCRA entity.

Compatible terms from the model remain candidates for selective reuse.

## Rejected Alternative: Represent VehiclePosition as the Vehicle

Another alternative was to map each GTFS-Realtime `VehiclePosition` directly
to a canonical vehicle entity without separating message semantics from
physical vehicle identity.

This was rejected because `VehiclePosition` represents real-time information
about a vehicle and its operation, not the persistent identity of the physical
transport resource itself.

Such a mapping would unnecessarily couple the semantic model to
GTFS-Realtime and would make integration with other operational data sources
more difficult.

## Rejected Alternative: Represent Vehicle Only Through MonitoredVehicleJourney

A further alternative was to avoid a separate vehicle entity and represent
the operating vehicle only as attributes of `MonitoredVehicleJourney`.

This was rejected because a physical vehicle persists across multiple
journeys and may have identity, registration, type, model, maintenance,
accessibility, ownership, and other information independent of any one
journey.

Vehicle and journey are therefore distinct semantic entities.

## Relationship to Previous Decisions

ADR-0003 establishes `ServiceJourney` as the semantic representation of a
scheduled passenger-carrying public transport operation.

ADR-0004 distinguishes scheduled, dated, and monitored journey concepts and
introduces `MonitoredVehicleJourney` for real-time operational state.

This ADR establishes the physical transport resource that performs such a
journey.

Together, the principal relationship is:

    ServiceJourney
          |
          v
    DatedVehicleJourney
          |
          v
    MonitoredVehicleJourney
          |
          | operatedByVehicle
          v
        Vehicle
          |
          +---- VehicleType
          |
          +---- VehicleModel

The exact NGSI-LD vocabulary and schema representation of these relationships
will be defined separately.

## References

- GTFS-Realtime Reference:
  https://gtfs.org/documentation/realtime/reference/

- GTFS-Realtime Protocol Buffer Definition:
  https://gtfs.org/documentation/realtime/proto/

- Transmodel Common Concepts Tutorial:
  https://transmodel-cen.eu/index.php/common-concepts/

- Transmodel Common Concepts Tutorial PDF:
  https://www.transmodel-cen.eu/wp-content/uploads/2015/01/TUTORIAL-Part1-3-v0.2-1.pdf

- Smart Data Models `Vehicle`:
  https://smartdatamodels.org/dataModel.Transportation/Vehicle

- Smart Data Models Transportation Models:
  https://smartdatamodels.org/index.php/list-of-data-models-2/

- ETSI NGSI-LD:
  https://cim.etsi.org/NGSI-LD/official/

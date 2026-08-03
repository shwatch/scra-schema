# MCP Mapping

## Status

Normative

---

## 1. Purpose

This document defines how canonical semantic concepts defined by SCRA Schema
may be exposed through the Model Context Protocol (MCP).

The purpose of this specification is to enable AI applications to discover,
retrieve, and reason about interoperable smart-city and mobility information
without making the underlying SCRA semantic models dependent on MCP.

SCRA semantic models define meaning.

This specification defines how that meaning may be accessed through MCP
Resources, Tools, and, where appropriate, Prompts.

---

## 2. Scope

This specification covers the MCP representation required for the initial
SCRA public transport profile and Mobility MCP proof of concept.

It defines principles for:

- exposing semantic information as MCP Resources;
- using Resource Templates for parameterized semantic access;
- exposing search and retrieval operations as MCP Tools;
- returning structured semantic results from Tools;
- preserving semantic identity and provenance;
- relating MCP resource URIs to SCRA semantic instances; and
- separating semantic data from executable operations.

This specification does not define:

- the MCP transport selected by an implementation;
- client user-interface behaviour;
- a particular MCP SDK;
- authentication or authorization policy;
- an NGSI-LD Broker architecture;
- GTFS parsing algorithms;
- storage architecture;
- prompt wording for individual applications; or
- a mandatory implementation language.

---

## 3. MCP Primitives

### 3.1 Resources

MCP Resources expose data or content that may be used as context by an MCP
client or AI application.

Each Resource is identified by a URI.

A Resource may expose:

- one SCRA semantic instance;
- a collection or view of semantic instances;
- a semantic model definition;
- a mapping specification;
- source provenance;
- a static reference document; or
- another contextual representation.

Resources are suitable when the primary purpose is to expose identifiable
information rather than execute a query or action.

---

### 3.2 Resource Templates

Resource Templates expose parameterized families of Resources through URI
templates.

They are suitable when a semantic instance can be addressed using known
parameters.

Typical examples include:

- a PublicTransportStop by semantic identifier;
- a PhysicalVehicle by semantic identifier;
- a ServiceJourney by semantic identifier;
- a DatedVehicleJourney by date and source identity;
- a MonitoredVehicleJourney by semantic identifier; or
- a collection associated with a route, stop, or operating day.

A Resource Template shall not be used to hide complex search behaviour that
is more appropriately represented as a Tool.

---

### 3.3 Tools

MCP Tools expose executable functions that may retrieve, filter, calculate,
or act upon external information.

Tools are suitable when an operation requires:

- search criteria;
- spatial or temporal filtering;
- integration of multiple sources;
- current-state retrieval;
- ranking;
- route or journey resolution;
- computation;
- validation; or
- another operation that cannot be represented as reading one known Resource
  URI.

Tools may return:

- structured content;
- human-readable text;
- Resource links;
- embedded Resources; or
- a combination of these forms.

---

### 3.4 Prompts

MCP Prompts may provide reusable user-selected interaction templates.

Prompts are not semantic models and shall not define the normative meaning of
SCRA concepts.

An implementation may provide prompts for tasks such as:

- describing nearby accessible stops;
- explaining the current progress of a journey;
- summarizing service disruption information; or
- preparing a passenger-facing journey-status explanation.

Prompt content is application-specific and outside the normative core of this
mapping.

---

## 4. Mapping Principles

### 4.1 Semantic Models Remain Authoritative

The normative meaning and identity of each concept are defined by the
applicable SCRA semantic model.

An MCP Resource, Tool, Tool result, or Prompt shall not redefine that meaning.

Where an MCP representation conflicts with the semantic model, the semantic
model remains authoritative.

---

### 4.2 No Automatic Entity-to-Resource Rule

A SCRA semantic Entity is not automatically required to appear as one
individually listed MCP Resource.

An implementation may expose a semantic instance through:

- a directly listed Resource;
- a Resource Template;
- a Tool result;
- a Resource link returned by a Tool; or
- an embedded Resource.

The selected mechanism shall depend on discoverability, scale, freshness,
access control, and client requirements.

---

### 4.3 Data and Operations Shall Be Separated

Reading a known semantic instance is conceptually different from searching
or calculating.

For example:

- reading a known PublicTransportStop is a Resource operation;
- searching for nearby stops is a Tool operation;
- reading a known MonitoredVehicleJourney is a Resource operation;
- finding active journeys for a route is a Tool operation.

An implementation shall not model an executable query as static Resource
content merely to avoid defining a Tool.

---

### 4.4 Source Structures Shall Not Become the MCP Model

MCP interfaces shall expose SCRA semantic concepts rather than raw
source-format nesting as the primary public contract.

For example, a Tool result should expose:

- MonitoredVehicleJourney;
- PhysicalVehicle;
- PublicTransportStop;
- DatedVehicleJourney; and
- associated semantic relationships,

rather than requiring the AI application to interpret raw GTFS-Realtime
protobuf structures.

Raw source information may be exposed separately for diagnostics,
traceability, or specialist use.

---

### 4.5 Semantic Identity Shall Be Preserved

MCP Resource URIs and structured Tool results shall preserve the semantic
identity rules defined by SCRA.

The following shall not independently define semantic identity:

- MCP request identifiers;
- Tool-call identifiers;
- GTFS-Realtime `FeedEntity.id`;
- source filenames;
- retrieval timestamps;
- temporary cache keys; or
- pagination cursors.

---

### 4.6 Current and Historical State Shall Be Distinguished

A current-state Resource may change over time while retaining the same URI
and semantic identity.

A historical observation or snapshot may require a distinct Resource URI or
an explicit temporal parameter.

The implementation shall make clear whether content represents:

- current state;
- state valid at a specified time;
- a historical observation;
- a forecast;
- a static plan; or
- source provenance.

---

### 4.7 Tools Shall Not Introduce Unsupported Semantics

A Tool shall not infer semantic certainty that is absent from its source
information.

For example, a Tool shall not:

- classify an event as observed solely because its timestamp is in the past;
- claim that a journey is on time solely because no TripUpdate exists;
- claim that a vehicle is absent solely because no recent position was
  returned; or
- treat uncertainty omitted by the source as zero uncertainty.

---

## 5. Resource URI Design

### 5.1 URI Requirement

Every MCP Resource shall have a URI.

The URI identifies the MCP-accessible Resource.

Where the Resource represents one semantic instance, its URI should preserve
or clearly correspond to that instance's stable semantic identity.

### 5.2 Custom URI Scheme

A Mobility MCP implementation may define a custom URI scheme that conforms
to URI syntax requirements.

An illustrative scheme is:

    mobility://<semantic-type>/<semantic-identity>

Illustrative examples include:

    mobility://public-transport-stop/operator-a/stop-10
    mobility://physical-vehicle/operator-a/vehicle-42
    mobility://service-journey/operator-a/trip-123
    mobility://dated-vehicle-journey/operator-a/2026-08-03/trip-123
    mobility://monitored-vehicle-journey/operator-a/2026-08-03/trip-123

These examples do not establish final normative URI syntax.

### 5.3 HTTPS Resources

An HTTPS URI may be used when the Resource is independently available through
the web and a client can retrieve it directly.

Where the Resource must be read through the MCP server, an implementation
should use an appropriate non-HTTP or custom URI scheme rather than implying
independent web availability.

### 5.4 URI Stability

A Resource URI representing a semantic instance should remain stable while
the instance's semantic identity remains unchanged.

Changes to:

- delay;
- location;
- speed;
- occupancy;
- prediction;
- current stop;
- report timestamp; or
- assigned vehicle

shall not normally create a new URI for the same MonitoredVehicleJourney.

---

## 6. Resource Representation

### 6.1 Content Format

A Resource representing SCRA semantic data should use a machine-readable
content format.

Recommended MIME types include:

- `application/ld+json`;
- `application/json`; or
- another explicitly documented semantic representation.

Human-readable Markdown or plain text may also be provided when useful to the
user or AI application.

### 6.2 Canonical Structured Content

Where possible, structured Resource content should preserve:

- semantic type;
- semantic identifier;
- properties;
- relationships;
- temporal meaning;
- provenance;
- source freshness; and
- applicable vocabulary context.

An NGSI-LD representation may be used as Resource content, but MCP does not
require NGSI-LD.

### 6.3 Resource Metadata

Resource metadata should include:

- stable URI;
- concise programmatic name;
- human-readable title where useful;
- clear description;
- MIME type;
- last-modified time where known; and
- annotations where appropriate.

Resource descriptions should explain the semantic content rather than the
source table or protobuf message from which it was derived.

---

## 7. Resource Templates

The following Resource Templates are candidates for the initial Mobility MCP
profile.

Their final URI syntax is implementation-specific.

| Semantic concept | Illustrative URI template |
|---|---|
| PublicTransportRoute | `mobility://public-transport-route/{authority}/{routeId}` |
| PublicTransportStop | `mobility://public-transport-stop/{authority}/{stopId}` |
| TransportStation | `mobility://transport-station/{authority}/{stationId}` |
| ServiceJourney | `mobility://service-journey/{authority}/{journeyId}` |
| JourneyPattern | `mobility://journey-pattern/{authority}/{patternId}` |
| PhysicalVehicle | `mobility://physical-vehicle/{authority}/{vehicleId}` |
| OperatingDay | `mobility://operating-day/{authority}/{date}` |
| DatedVehicleJourney | `mobility://dated-vehicle-journey/{authority}/{date}/{journeyId}` |
| MonitoredVehicleJourney | `mobility://monitored-vehicle-journey/{authority}/{date}/{journeyId}` |

A source identifier such as `trip_id` or `stop_id` shall be interpreted within
the authority and dataset scope defined by the implementation.

---

## 8. Core Resources

### 8.1 PublicTransportStop

A known PublicTransportStop may be exposed as a Resource.

Its content may include:

- semantic identifier;
- name;
- location;
- stop type;
- accessibility information;
- parent TransportStation;
- served routes; and
- provenance.

Nearby-stop search shall be implemented as a Tool rather than as one fixed
Resource unless a stable predefined collection is intended.

---

### 8.2 ServiceJourney

A known ServiceJourney may be exposed as a Resource.

Its content may include:

- relationship to PublicTransportRoute;
- relationship to JourneyPattern;
- calendar applicability;
- destination information;
- scheduled stop sequence;
- scheduled timing information; and
- provenance.

---

### 8.3 DatedVehicleJourney

A known DatedVehicleJourney may be exposed as a Resource.

Its content may include:

- relationship to ServiceJourney where applicable;
- OperatingDay;
- operational status;
- assigned PhysicalVehicle where known;
- cancellation or replacement state; and
- provenance.

---

### 8.4 MonitoredVehicleJourney

A known MonitoredVehicleJourney may be exposed as a Resource.

Its content may include:

- relationship to DatedVehicleJourney;
- relationship to PhysicalVehicle;
- current position or progress;
- current stop;
- next stop;
- delay;
- occupancy;
- current operational status;
- observation time;
- estimated passing information; and
- source freshness.

The Resource may change as new realtime information arrives while retaining
the same URI.

---

### 8.5 PhysicalVehicle

A known PhysicalVehicle may be exposed as a Resource.

Its content may include:

- semantic identifier;
- label;
- registration or fleet information;
- accessibility characteristics;
- current observed location;
- occupancy;
- current journey assignment; and
- observation time.

---

## 9. Core Tools

The initial Mobility MCP proof of concept should expose a small set of
high-value Tools rather than one Tool for every source table or semantic
Entity Type.

### 9.1 Search Nearby Stops

Illustrative name:

    search_nearby_stops

Purpose:

Find PublicTransportStops or TransportStations near a geographic location.

Typical inputs include:

- latitude;
- longitude;
- radius;
- transport mode;
- accessibility requirements; and
- result limit.

Typical output includes semantic stop summaries and Resource links.

---

### 9.2 Search Active Journeys

Illustrative name:

    search_active_journeys

Purpose:

Find MonitoredVehicleJourneys matching route, stop, destination, location, or
time criteria.

Typical inputs include:

- PublicTransportRoute identifier;
- PublicTransportStop identifier;
- destination;
- geographic area;
- current time; and
- result limit.

Typical output includes journey summaries and Resource links.

---

### 9.3 Get Monitored Journey

Illustrative name:

    get_monitored_journey

Purpose:

Retrieve the current semantic state of one MonitoredVehicleJourney when its
semantic identity or sufficient source identity is known.

The Tool may return structured semantic content and a Resource link to the
corresponding current-state Resource.

---

### 9.4 Get Vehicle Position

Illustrative name:

    get_vehicle_position

Purpose:

Retrieve the latest available position and observation metadata for one
PhysicalVehicle or MonitoredVehicleJourney.

The Tool shall distinguish:

- vehicle identity;
- monitored journey identity;
- observed location;
- observation time; and
- source freshness.

---

### 9.5 Get Arrivals

Illustrative name:

    get_arrivals

Purpose:

Retrieve estimated, target, timetabled, or observed arrival information for a
PublicTransportStop.

The Tool shall preserve the distinction among timing semantics.

Until the normative passing-time family is completed, Tool results shall
clearly identify the source semantics and shall not classify values more
strongly than supported by the source.

---

## 10. Tool Inputs

Tool input schemas shall use semantic concepts wherever practical.

For example, a Tool should request:

- semantic stop identifier;
- semantic route identifier;
- geographic coordinates;
- operating date; or
- semantic journey identifier,

rather than exposing raw protobuf field paths.

Source-specific identifiers may be accepted where necessary, but their scope
shall be explicit.

Tool descriptions shall explain:

- expected identifier authority;
- timezone assumptions;
- units;
- optional versus required inputs;
- result scope; and
- error behaviour.

---

## 11. Tool Results

### 11.1 Structured Results

Tool results should return structured semantic data when practical.

The structure should identify:

- semantic type;
- semantic identifier;
- relevant properties;
- relevant relationships;
- timestamps;
- provenance; and
- freshness.

### 11.2 Human-Readable Summary

A Tool may additionally return a concise human-readable summary.

The summary shall not replace the structured semantic result when the
structured form is required for reliable AI reasoning or downstream use.

### 11.3 Resource Links

A Tool should return Resource links when the result identifies semantic
instances that can subsequently be read as MCP Resources.

For example, `search_active_journeys` may return links to individual
MonitoredVehicleJourney Resources.

### 11.4 Embedded Resources

A Tool may embed a Resource when immediate complete context is useful and the
payload size is appropriate.

The embedded Resource shall use the same semantic URI and representation as
the independently readable Resource where one exists.

---

## 12. Illustrative Tool Result

The following example is informative.

```json
{
  "content": [
    {
      "type": "text",
      "text": "One active journey was found."
    },
    {
      "type": "resource_link",
      "uri": "mobility://monitored-vehicle-journey/operator-a/2026-08-03/trip-123",
      "name": "monitored-journey-trip-123",
      "title": "Route 12 journey to Central Station",
      "description": "Current monitored state of the selected public transport journey",
      "mimeType": "application/ld+json"
    }
  ],
  "structuredContent": {
    "journeys": [
      {
        "id": "urn:ngsi-ld:MonitoredVehicleJourney:operator-a:20260803:trip-123",
        "type": "MonitoredVehicleJourney",
        "delaySeconds": 120,
        "observedAt": "2026-08-03T06:20:00Z"
      }
    ]
  }
}
```

This example does not establish final Tool names, result schemas, URI syntax,
or SCRA vocabulary terms.

---

## 13. Relationship to NGSI-LD

An MCP server may obtain semantic information from an NGSI-LD Broker.

However, MCP Resources and Tools shall expose SCRA semantic meaning rather
than Broker-specific implementation details.

Conceptually:

    GTFS / GTFS-Realtime / SIRI / Other Sources
                      |
                      v
              SCRA Semantic Model
                      |
              +-------+-------+
              |               |
              v               v
           NGSI-LD           MCP
          Representation   Access Interface

MCP and NGSI-LD are separate technology-specific mappings of the same
semantic foundation.

MCP shall not be treated as a replacement for the SCRA semantic model or for
NGSI-LD.

---

## 14. Realtime Update Behaviour

### 14.1 Current-State Resources

A current-state Resource may be updated as new realtime information becomes
available.

If supported by the MCP server and client, Resource subscriptions may be used
to notify clients that the Resource has changed.

### 14.2 Resource List Changes

A change in operational state does not necessarily mean that the list of
Resources has changed.

A list-change notification is appropriate when the set of exposed Resources
changes, not merely when one Resource's content changes.

### 14.3 Monitoring Expiration

The server shall define when a MonitoredVehicleJourney is no longer
considered current.

Expiration of monitoring does not automatically delete:

- the DatedVehicleJourney;
- the ServiceJourney;
- the PhysicalVehicle; or
- historical observations.

The server may stop exposing a current-state Resource or may return an
explicit non-current status according to its profile.

---

## 15. Provenance and Freshness

Resource content and Tool results should preserve sufficient information to
assess source and freshness.

Relevant information may include:

- source authority;
- source feed;
- source standard;
- source timestamp;
- observation timestamp;
- retrieval timestamp;
- mapping profile version;
- last update time; and
- stale-data status.

AI applications shall not be expected to infer freshness from request time
alone.

---

## 16. Errors and Unresolved Semantics

An MCP implementation shall return an explicit error or unresolved result
when required semantic information cannot be established.

Examples include:

- unknown semantic identifier;
- ambiguous `trip_id` without an OperatingDay;
- repeated `stop_id` without sufficient pattern-point context;
- stale realtime information;
- vehicle known but journey assignment unknown;
- journey known but PhysicalVehicle unknown; and
- insufficient evidence to classify a passing value as observed.

An unresolved relationship is preferable to an invented semantic value.

---

## 17. Security and Access Control

Resources and Tools may expose operationally or commercially sensitive
information.

An implementation shall apply appropriate:

- authentication;
- authorization;
- URI validation;
- input validation;
- rate limiting;
- source-access controls; and
- audit logging.

Tool execution and data access shall remain subject to user and application
control.

This specification does not define a specific authorization architecture.

---

## 18. Initial Proof-of-Concept Profile

The initial Mobility MCP proof of concept shall prioritize the following
capabilities:

1. Read or retrieve current MonitoredVehicleJourney information.
2. Relate a MonitoredVehicleJourney to its PhysicalVehicle where known.
3. Return current observed vehicle location and observation time.
4. Relate the monitored journey to its ServiceJourney,
   PublicTransportRoute, and PublicTransportStops.
5. Retrieve predicted stop arrival information where available.
6. Preserve source provenance and freshness.
7. Produce information suitable for natural-language passenger guidance.
8. Support accessibility-oriented queries, particularly for visually
   impaired passengers.

The proof of concept is not required to implement every SCRA semantic model
as a separately listed Resource.

---

## 19. Deferred Items

The following items are deferred to later specifications:

- final MCP Resource URI scheme;
- final Resource Template definitions;
- final Tool names;
- final Tool input and output schemas;
- prompts;
- alerts and disruption Tools;
- historical journey Resources;
- Resource subscription profile;
- authorization profile;
- multilingual output profile;
- pagination conventions;
- semantic result validation; and
- normative passing-time result structures.

---

## 20. Conformance

An MCP mapping conforms to this specification when it:

- preserves the normative meaning and identity of SCRA semantic concepts;
- distinguishes Resources, Tools, and Prompts according to their roles;
- does not automatically expose every semantic Entity as one listed
  Resource;
- uses Resources or Resource Templates for identifiable contextual data;
- uses Tools for search, retrieval, filtering, integration, and computation;
- exposes semantic concepts rather than raw source-format nesting as the
  primary interface;
- preserves semantic identity across realtime state updates;
- distinguishes current, planned, predicted, observed, and historical
  information;
- preserves provenance and freshness;
- returns unresolved information explicitly rather than inventing semantic
  certainty;
- provides appropriate Resource URIs and semantic result structures; and
- remains independent of any particular MCP SDK, client, transport, or
  storage architecture.

---

## 21. References

- Model Context Protocol Specification:
  https://modelcontextprotocol.io/specification/2025-11-25

- MCP Resources:
  https://modelcontextprotocol.io/specification/2025-11-25/server/resources

- MCP Tools:
  https://modelcontextprotocol.io/specification/2025-11-25/server/tools

- MCP Prompts:
  https://modelcontextprotocol.io/specification/2025-11-25/server/prompts

- SCRA Semantic Modeling Principles:
  ../specifications/semantic-modeling-principles.md

- SCRA Semantic Models:
  ../models/index.md

- SCRA GTFS Static Mapping:
  gtfs-static.md

- SCRA GTFS-Realtime Mapping:
  gtfs-realtime.md

- SCRA NGSI-LD Mapping:
  ngsi-ld.md

---

© SCRA Project
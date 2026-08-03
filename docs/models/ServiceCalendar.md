# ServiceCalendar

## Status

Normative

---

## Definition

A ServiceCalendar represents the calendar context through which DayTypes are
assigned to specific OperatingDays.

It connects recurring, date-independent service conditions with concrete
operational-day occurrences.

A ServiceCalendar does not itself represent a DayType, an OperatingDay, or a
transport service.

It provides the calendar structure required to determine which DayTypes and
time-dependent service conditions apply to particular OperatingDays.

---

## Identity

The semantic identity of a ServiceCalendar is established by the calendar
arrangement maintained within a defined authority, operational context, and
validity scope.

A ServiceCalendar remains the same semantic instance when:

- individual DayType assignments are added or corrected;
- additional OperatingDays are included;
- future calendar information is extended;
- exceptional dates are added or removed;
- the services referring to the calendar are revised; or
- a new published version describes the same continuing calendar arrangement.

Changes to calendar content do not necessarily create a new ServiceCalendar.

A distinct ServiceCalendar exists when the underlying calendar arrangement
belongs to a different operational authority, operational context, or
independently managed calendar system.

Version identifiers, publication timestamps, source record identifiers, and
dataset identifiers do not by themselves determine semantic identity.

Implementation-specific identifier construction shall be defined by the
relevant mapping specification.

---

## Motivation

Recurring transport plans are generally defined for logical types of day,
such as weekdays, Saturdays, public holidays, or school days.

Operational systems, however, require those logical classifications to be
resolved against specific dated OperatingDays.

The applicable classification may also change because of:

- public holidays;
- school calendars;
- special events;
- seasonal arrangements;
- temporary service changes; or
- locally defined operating conditions.

ServiceCalendar provides the semantic context that connects reusable DayTypes
with concrete OperatingDays without embedding individual dates directly into
every recurring service definition.

---

## Characteristics

A ServiceCalendar:

- provides a calendar context for public transport operation;
- associates DayTypes with OperatingDays;
- may cover a defined validity period;
- may contain regular calendar rules;
- may contain explicit date assignments or exceptions;
- may be maintained by an operational or planning authority;
- may be referenced by multiple time-dependent transport concepts;
- is independent of any one ServiceJourney;
- is distinct from a DayType;
- is distinct from an OperatingDay; and
- is independent of implementation-specific calendar tables or message
  structures.

---

## Relationships

### DayType

A ServiceCalendar assigns one or more DayTypes to OperatingDays.

A DayType represents a logical, date-independent classification.

The ServiceCalendar determines the concrete operational days to which that
classification applies within its calendar context.

A DayType may be reused by more than one ServiceCalendar.

---

### OperatingDay

A ServiceCalendar includes or references OperatingDays within its applicable
scope.

Each OperatingDay represents one specific operational-day occurrence.

The ServiceCalendar may associate an OperatingDay with one or more DayTypes
or other relevant calendar conditions.

The OperatingDay remains a distinct semantic concept and is not merely a
calendar entry.

---

### Property of Day

A ServiceCalendar may use properties of day to determine or explain the
DayTypes assigned to an OperatingDay.

Such properties may include:

- day of week;
- public holiday;
- school day;
- school holiday;
- market day;
- seasonal condition; or
- another locally relevant condition.

A property of day is not itself a ServiceCalendar.

---

### Time-Dependent Service Concept

A ServiceCalendar may support the resolution of when a time-dependent
transport concept is applicable.

Such concepts may include:

- ServiceJourney;
- fare product;
- service facility;
- operating pattern; or
- another planned transport concept.

The recurring concept may refer to a DayType, while the ServiceCalendar
resolves that DayType to specific OperatingDays.

---

### Validity Condition

A ServiceCalendar may have a validity condition or validity period.

The validity condition determines when the calendar arrangement is applicable
or authoritative.

A change of validity period does not automatically establish a different
semantic calendar if it represents the continuation of the same managed
calendar arrangement.

---

## Typical Properties

Typical semantic properties include:

- identifier
- name
- description
- operationalAuthority
- validityStart
- validityEnd
- timezone
- dayTypeAssignments
- operatingDays
- propertiesOfDay
- calendarVersion
- publicationTimestamp

The presence of a typical property does not make it mandatory.

`calendarVersion` and `publicationTimestamp` describe a representation or
publication of the calendar and do not by themselves determine semantic
identity.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Origin

This semantic concept is adopted primarily from:

- Transmodel — `SERVICE CALENDAR`
- Transmodel — `DAY TYPE`
- Transmodel — `OPERATING DAY`
- Transmodel — `PROPERTY OF DAY`
- Transmodel — validity concepts

Transmodel uses a ServiceCalendar to assign DayTypes to OperatingDays.

This allows recurring transport plans to remain independent of specific
calendar dates while still being resolved into concrete operational plans.

SCRA Schema adopts this separation without changing its fundamental meaning.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| Transmodel `SERVICE CALENDAR` | ServiceCalendar |
| GTFS `calendar.txt` | Recurring calendar rule contributing to ServiceCalendar |
| GTFS `calendar_dates.txt` | Explicit date assignment or exception contributing to ServiceCalendar |
| GTFS `service_id` | Source identifier for a resolved set of applicable service dates |
| NeTEx service calendar structures | ServiceCalendar |
| SIRI service-day context | Reference to resolved OperatingDay or ServiceCalendar context |

A GTFS `service_id` shall not automatically be treated as equivalent to one
ServiceCalendar, DayType, or OperatingDay.

In GTFS, a `service_id` identifies a set of dates on which service is
available.

That date set may be defined by:

- a recurring weekday pattern and validity interval in `calendar.txt`;
- explicit additions or removals in `calendar_dates.txt`; or
- a complete enumeration of applicable dates in `calendar_dates.txt`.

A conforming mapping shall resolve the combined semantics of these source
records before establishing correspondence with:

- ServiceCalendar;
- DayType;
- OperatingDay; and
- the time-dependent service concept using the `service_id`.

The mapping shall also define:

- source authority;
- identifier scope;
- timezone;
- validity interval;
- treatment of exceptions;
- handling of datasets without `calendar.txt`; and
- handling of multiple source records that resolve to equivalent date sets.

This section is informative and does not form part of the normative semantic
definition.

---

## See Also

- DayType
- OperatingDay
- ServiceJourney
- DatedVehicleJourney

---

## References

- Transmodel Common Concepts Tutorial:
  https://transmodel-cen.eu/index.php/common-concepts/

- Transmodel at a Glance:
  https://transmodel-cen.eu/index.php/transmodel-at-a-glance/

- Transmodel Operations Monitoring and Control Tutorial:
  https://transmodel-cen.eu/index.php/oper/

- GTFS Schedule Reference:
  https://gtfs.org/documentation/schedule/reference/

---

© SCRA Project

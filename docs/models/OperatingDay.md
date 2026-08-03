# DayType

## Status

Normative

---

## Definition

A DayType represents a logical classification of operating days that share
the same transport-relevant characteristics.

A DayType is independent of any specific calendar date.

It may describe conditions such as:

- day of week;
- working day or non-working day;
- school day or school holiday;
- public holiday;
- market day;
- seasonal condition; or
- another characteristic relevant to transport demand or operations.

A DayType may combine multiple day-related properties.

---

## Identity

The semantic identity of a DayType is established by the transport-relevant
classification that it represents.

A DayType remains the same semantic instance when:

- it is assigned to different OperatingDays;
- the calendar period in which it is used changes;
- additional OperatingDays are associated with it; or
- the services using it are revised.

A change to the defining combination of day-related characteristics may
result in a different DayType.

A particular calendar date does not determine the identity of a DayType.

Implementation-specific identifiers are defined by the relevant mapping
specification.

---

## Motivation

Public transport services are commonly planned according to recurring types
of day rather than separately for every calendar date.

For example, the same service plan may apply to:

- ordinary weekdays;
- Saturdays;
- Sundays and public holidays;
- school days; or
- special seasonal periods.

Representing these recurring conditions as DayTypes allows service plans to
remain independent of specific dates.

The applicable DayType for a particular date is established through the
ServiceCalendar and OperatingDay model.

---

## Characteristics

A DayType:

- represents a logical classification rather than a calendar date;
- may combine multiple day-related properties;
- may apply to multiple OperatingDays;
- may be used by multiple service plans;
- is independent of any one ServiceJourney;
- is independent of real-time operational conditions; and
- may be reused across different validity periods.

---

## Relationships

### Property of Day

A DayType may be characterised by one or more properties of day.

Each property describes a condition relevant to transport demand or
operations, such as:

- weekday;
- public holiday;
- school holiday;
- market day; or
- another locally relevant condition.

The combination of such properties determines the meaning of the DayType.

---

### OperatingDay

A DayType may be assigned to one or more OperatingDays.

An OperatingDay represents a particular operational day, whereas a DayType
represents the logical classification applied to that day.

The assignment of a DayType to an OperatingDay does not change the identity
of either concept.

---

### ServiceCalendar

A ServiceCalendar establishes the assignment of DayTypes to OperatingDays
within a defined calendar context.

The DayType remains independent of the specific dates managed by the
ServiceCalendar.

---

### ServiceJourney

A ServiceJourney or another time-dependent service concept may be associated
with one or more DayTypes.

This relationship indicates the kinds of operating day on which the planned
service is intended to apply.

The concrete dated operation is represented separately by a
DatedVehicleJourney.

---

## Typical Properties

Typical semantic properties include:

- identifier
- name
- description
- propertiesOfDay
- validityCondition

The presence of a typical property does not make it mandatory.

This semantic model does not prescribe implementation-specific property
names, value encodings, or data structures.

---

## Origin

This semantic concept is adopted primarily from:

- Transmodel — `DAY TYPE`
- Transmodel — `PROPERTY OF DAY`
- Transmodel — `SERVICE CALENDAR`

Transmodel defines a DayType as a logical type of day independent of a
specific calendar date.

A DayType may be characterised by a combination of properties that influence
transport demand or operating conditions.

SCRA Schema adopts this distinction without changing its fundamental
meaning.

---

## Mapping Guidance

Typical mappings include:

| Source | Semantic Mapping |
|--------|------------------|
| Transmodel `DAY TYPE` | DayType |
| Transmodel `PROPERTY OF DAY` | Characteristic of DayType |
| GTFS `calendar.txt` weekday pattern | Possible source for a DayType |
| GTFS `service_id` | Source identifier for a recurring service-date pattern |

A GTFS `service_id` shall not automatically be treated as equivalent to a
DayType.

Depending on the dataset, a `service_id` may identify a set of applicable
dates produced from:

- a weekly pattern in `calendar.txt`;
- exceptions in `calendar_dates.txt`; or
- an explicit list of dates in `calendar_dates.txt`.

The mapping shall therefore interpret the combined calendar semantics before
establishing correspondence with Day

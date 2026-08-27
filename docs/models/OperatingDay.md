# OperatingDay

## Status

Normative

---

## Definition

An OperatingDay represents one specific calendar date as an operational day
for public transport service.

It provides the dated operational context in which scheduled or exceptional
transport service is interpreted.

An OperatingDay is distinct from a DayType. A DayType classifies kinds of days,
whereas an OperatingDay identifies one particular operational date.

---

## Identity

The semantic identity of an OperatingDay is established by the operational
calendar date within the applicable transport or service-calendar context.

An OperatingDay remains the same semantic instance when:

- the set of services operating on that date changes;
- additional or cancelled DatedVehicleJourneys are introduced;
- a different DayType classification is assigned;
- operational observations are received; or
- real-time updates modify the state of journeys on that day.

A different operational calendar date represents a different OperatingDay.

Where more than one service-calendar scope exists for the same civil date, the
applicable profile shall define the additional scope needed to disambiguate
OperatingDay identity.

---

## Motivation

Public transport planning distinguishes recurring service patterns from the
actual dated operation of those patterns.

A DayType describes a recurring logical classification such as weekday,
Saturday, Sunday, holiday, or another transport-relevant category.

An OperatingDay provides the concrete date to which such classifications and
service-calendar rules are applied.

This distinction allows SCRA to represent dated journeys independently from
their recurring timetable definitions.

---

## Characteristics

An OperatingDay:

- represents one specific operational date;
- may be assigned one or more DayType classifications according to the
  applicable calendar model;
- exists within a ServiceCalendar or equivalent calendar-resolution context;
- may contain or be referenced by multiple DatedVehicleJourneys;
- is independent of the real-time state of those journeys; and
- shall not be treated as equivalent to a recurring DayType.

---

## Relationships

### DayType

An OperatingDay may be associated with an applicable DayType.

The DayType describes the logical classification of the day; the OperatingDay
identifies the actual operational date.

Assigning or changing a DayType classification does not change the identity of
the OperatingDay.

---

### ServiceCalendar

An OperatingDay may be resolved within one ServiceCalendar.

The ServiceCalendar provides the recurring validity rules, exception dates, or
other calendar semantics used to determine which service definitions apply to
the OperatingDay.

A ServiceCalendar is not itself an OperatingDay and shall not replace the dated
identity of the operational date.

---

### DatedVehicleJourney

One OperatingDay may be referenced by zero or more DatedVehicleJourneys.

Each DatedVehicleJourney represents a planned journey occurrence in the context
of one specific OperatingDay.

A DatedVehicleJourney may be added, cancelled, replaced, or otherwise altered
without changing the identity of the OperatingDay.

---

## Typical Properties

Typical semantic properties may include:

- calendarDate;
- localDate;
- serviceDate;
- timezone;
- calendarScope; and
- source identifier.

These names are informative examples only unless separately defined by a
normative SCRA vocabulary declaration or an applicable profile.

---

## Conformance Requirements

A conforming OperatingDay representation shall:

- identify one specific operational calendar date;
- remain semantically distinct from DayType;
- provide sufficient scope to disambiguate the date where multiple calendar
  contexts are applicable; and
- be usable as the dated operational context for DatedVehicleJourney.

Where a ServiceCalendar is used to resolve service applicability, the mapping
shall preserve the difference between:

- recurring calendar rules;
- calendar exceptions;
- the resolved OperatingDay; and
- the DatedVehicleJourneys operating on that day.

---

## Origin

The concept is harmonized with public-transport scheduling models that
distinguish recurring day classifications from dated operational days.

SCRA uses OperatingDay as the canonical dated service-operation context.

---

## Mapping Guidance

### GTFS Static

GTFS does not publish an explicit OperatingDay entity.

An OperatingDay is resolved from a concrete service date together with the
calendar semantics of the applicable GTFS service.

For a GTFS service, the mapping shall evaluate the combined effect of:

- `calendar.txt`, where present, for recurring weekday validity;
- `calendar_dates.txt` for added or removed service dates; and
- the target `service_date` or equivalent concrete operational date.

The resolved operational date may then be represented as an OperatingDay.

A GTFS `service_id` shall not by itself be treated as an OperatingDay. It
identifies a service-calendar pattern or service-date rule set rather than one
specific operational date.

Likewise, a DayType inferred from recurring weekday or holiday semantics shall
not replace the OperatingDay.

### GTFS-Realtime

Where GTFS-Realtime identifies a dated journey using `start_date`,
service-date context, or an equivalent dated discriminator, that information
may contribute to resolution of the applicable OperatingDay.

The mapping shall first resolve the dated operational context and then relate
the corresponding DatedVehicleJourney to that OperatingDay.

Real-time updates shall not change OperatingDay identity merely because journey
status, delay, assignment, or prediction data changes.

---

## Mapping Summary

| Source concept | SCRA semantic mapping |
|---|---|
| GTFS concrete service date | OperatingDay date basis |
| GTFS `calendar.txt` | Recurring service-calendar validity |
| GTFS `calendar_dates.txt` | Added/removed service-date exceptions |
| GTFS `service_id` | Source identifier for calendar/service validity, not OperatingDay identity by itself |
| GTFS-Realtime `start_date` or equivalent dated discriminator | Input to OperatingDay resolution |
| SCRA DayType | Logical classification applicable to OperatingDay |
| SCRA ServiceCalendar | Calendar context used to resolve OperatingDay |
| SCRA DatedVehicleJourney | Planned journey occurrence on OperatingDay |

---

## See Also

- DayType
- ServiceCalendar
- ServiceJourney
- DatedVehicleJourney
- MonitoredVehicleJourney

---

© SCRA Project

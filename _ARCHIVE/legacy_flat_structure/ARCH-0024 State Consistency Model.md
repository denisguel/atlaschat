# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0024
# DOCUMENT    : State Consistency Model
# VERSION     : 1.0
# STATUS      : ACCEPTED
# CLASS       : Core Architecture
#
# NOTE
#
# This document is NEW.
#
# It does NOT replace any previous specification.
#
# This specification resolves all ambiguity regarding:
#
# • Single Source of Truth
# • Events
# • State
# • Event Sourcing
# • CQRS readiness
#
# Future versions of:
#
# - RFC-0005
# - ARCH-0015
# - ARCH-0020
#
# MUST reference this document.
#
# ============================================================================

# ARCH-0024

# STATE CONSISTENCY MODEL

---

# 1. Purpose

This document defines the official consistency model of ATLAS.

Its objective is to eliminate ambiguity regarding:

- the Single Source of Truth (SSOT)
- event generation
- persistence
- synchronization
- distributed consistency
- Event Sourcing adoption

Every architectural decision involving data persistence MUST comply with this specification.

---

# 2. Motivation

Earlier architectural drafts mixed two different concepts:

- relational persistence
- event sourcing

Although compatible, they are not identical.

Attempting to implement both simultaneously during the MVP would introduce unnecessary complexity, synchronization challenges and operational risk.

This document formally defines the architecture adopted by ATLAS.

---

# 3. Architectural Principle

The following statement is immutable.

```
PostgreSQL
IS

THE

ONLY

SINGLE

SOURCE

OF

TRUTH.
```

Nothing else.

---

# 4. Definition of Truth

Truth means:

The authoritative representation of the current operational state.

Examples:

Reservations

Guests

Properties

Availability

Prices

Tasks

Invoices

Organizations

Users

Permissions

Everything lives in PostgreSQL.

---

# 5. Event Philosophy

Events are NOT truth.

Events describe something that already happened.

Example

```
ReservationConfirmed
```

The reservation already exists.

The event is merely a historical fact.

---

# 6. Official Architecture

```
Request

↓

Business Validation

↓

Domain Service

↓

PostgreSQL

↓

Commit

↓

Domain Event

↓

Subscribers

↓

Read Models

↓

Notifications

↓

Analytics
```

Persistence always occurs before publication.

---

# 7. Why PostgreSQL First

Writing events before persistence introduces distributed consistency problems.

Examples:

Event published.

↓

Database fails.

↓

Subscribers believe the reservation exists.

↓

Reservation does not exist.

This architecture is forbidden.

---

# 8. Commit Before Publish

ATLAS follows:

```
Validate

↓

Persist

↓

Commit

↓

Publish Event
```

Never:

```
Publish

↓

Persist
```

---

# 9. Domain Events

Events represent completed business facts.

Examples:

ReservationCreated

ReservationCancelled

GuestCheckedIn

GuestCheckedOut

PaymentCaptured

CleaningAssigned

PriceUpdated

CouponGenerated

OwnerInvited

OrganizationCreated

---

# 10. Event Consumers

Events may be consumed by:

Analytics

Notifications

n8n

Growth Brain

Recommendation Engines

Search Indexes

Caches

Audit Systems

No consumer owns operational truth.

---

# 11. Read Models

Read Models are projections.

Examples:

Dashboard statistics

Revenue charts

Occupancy calendars

Cleaning queues

Forecasts

Search indexes

They may be regenerated at any time.

Because truth remains in PostgreSQL.

---

# 12. Event Store

The MVP SHALL NOT implement Event Sourcing.

Instead,

events are stored as immutable historical records.

Purpose:

Audit

Replay

Debugging

Analytics

Future migration

Events are historical artifacts—not authoritative state.

---

# 13. Event Replay

Future versions may rebuild:

Search indexes

Dashboards

Analytics

Recommendation models

Caches

from historical events.

Operational state shall never be rebuilt from events during the MVP.

---

# 14. Consistency Model

ATLAS adopts:

Strong consistency

inside transactional operations.

Eventual consistency

for projections.

This separation maximizes reliability while preserving scalability.

---

# 15. Transaction Boundary

One business operation.

↓

One database transaction.

↓

One commit.

↓

One event publication.

This boundary is immutable.

---

# 16. Outbox Pattern

Event publication SHALL use the Transactional Outbox Pattern.

Transaction

↓

Business Tables

+

Outbox Table

↓

Commit

↓

Background Publisher

↓

Event Bus

This guarantees that committed data and published events remain synchronized.

---

# 17. Failure Recovery

If publication fails:

Database remains correct.

Outbox retains pending event.

Publisher retries automatically.

No business data is lost.

---

# 18. Idempotency

Every event SHALL contain:

Event ID

Aggregate ID

Version

Timestamp

Correlation ID

Consumers MUST be idempotent.

Duplicate delivery is acceptable.

Duplicate side effects are forbidden.

---

# 19. Versioning

Events are immutable.

Corrections create new events.

Never modify historical events.

Example

Incorrect price

↓

PriceCorrected event

Never edit

PriceUpdated event.

---

# 20. Event Ordering

Ordering is guaranteed only within the same aggregate.

Reservation

↓

Created

↓

Confirmed

↓

CheckedIn

↓

CheckedOut

Cross-aggregate ordering is not guaranteed.

---

# 21. CQRS Readiness

The architecture is CQRS-ready.

However,

the MVP uses one transactional database.

Read projections evolve independently.

Separate databases are unnecessary until justified by scale.

---

# 22. Event Sourcing Readiness

Future migration to Event Sourcing remains possible because:

events are immutable

events contain versions

aggregates have identities

audit history exists

However,

Event Sourcing SHALL NOT be implemented before clear business justification.

---

# 23. Anti-Patterns

Forbidden:

Two Sources of Truth.

Publishing before commit.

Business logic inside subscribers.

Events updating business state.

Analytics modifying operational tables.

Subscribers writing directly into transactional entities.

---

# 24. Success Metrics

Healthy production indicators:

100% transactional consistency.

Zero orphan events.

Zero lost events.

Automatic event replay.

Recoverable projections.

Strong operational integrity.

---

# 25. Final Statement

ATLAS distinguishes operational truth from historical facts.

PostgreSQL is the single authoritative representation of business state.

Events exist to communicate, audit, analyze and integrate—not to replace operational persistence.

This separation dramatically simplifies the MVP while preserving a clear migration path toward CQRS and Event Sourcing when business scale genuinely requires them.
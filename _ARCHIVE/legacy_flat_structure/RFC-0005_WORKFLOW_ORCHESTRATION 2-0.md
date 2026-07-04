# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : RFC-0005
# DOCUMENT    : Event-Driven Architecture
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Architecture
#
# REPLACES
#
# RFC-0005_EVENT_DRIVEN_ARCHITECTURE.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# ARCH-0022 Business Decision Validation Layer
# ARCH-0024 State Consistency Model
# ARCH-0025 MVP Physical Architecture
# ARCH-0026 Workflow Responsibility Model
#
# ============================================================================

# RFC-0005

# EVENT-DRIVEN ARCHITECTURE

---

# 1. Purpose

This document defines how Domain Events are produced, published and consumed throughout ATLAS.

Its objective is to enable asynchronous communication between architectural components while preserving transactional consistency and a single authoritative operational state.

---

# 2. Motivation

Modern hospitality platforms interact with numerous internal modules and external systems.

Examples include:

- notifications;
- OTA synchronization;
- analytics;
- automation;
- audit;
- reporting;
- recommendation engines.

Coupling these capabilities directly to transactional operations reduces reliability and scalability.

Domain Events provide loose coupling without sacrificing consistency.

---

# 3. Core Principles

The following principles are immutable.

### Principle 1

PostgreSQL is the Single Source of Truth.

### Principle 2

Events describe facts.

They never define operational truth.

### Principle 3

Events are immutable.

### Principle 4

Events are published only after successful transaction commit.

### Principle 5

Consumers never modify transactional state directly.

---

# 4. Architectural Overview

```
User Request

↓

Business Validation

↓

Domain Service

↓

PostgreSQL Transaction

↓

Commit

↓

Outbox

↓

Event Publisher

↓

Event Bus

↓

Consumers
```

Every event originates from a committed business transaction.

---

# 5. Event Definition

A Domain Event represents a completed business fact.

Examples:

ReservationCreated

ReservationConfirmed

ReservationCancelled

GuestCheckedIn

GuestCheckedOut

CleaningAssigned

PaymentCaptured

InvoiceIssued

PriceUpdated

OwnerInvited

OrganizationCreated

---

# 6. Event Lifecycle

Every Domain Event follows the same lifecycle.

```
Business Action

↓

Validation

↓

Persistence

↓

Commit

↓

Outbox

↓

Publication

↓

Consumption
```

No event is published before persistence succeeds.

---

# 7. Transaction Boundary

Every business operation defines exactly one transactional boundary.

Inside the transaction:

- business validation;
- state modification;
- outbox registration.

Outside the transaction:

- notifications;
- integrations;
- analytics;
- workflows.

---

# 8. Transactional Outbox

ATLAS SHALL implement the Transactional Outbox Pattern.

During the same transaction:

Business Tables

+

Outbox Table

↓

Commit

↓

Background Publisher

↓

Event Bus

This guarantees reliable event publication.

---

# 9. Event Structure

Every event SHALL contain:

Event ID

Aggregate ID

Aggregate Type

Version

Timestamp

Organization ID

Correlation ID

Causation ID

Event Type

Payload

Metadata

The structure is versioned.

---

# 10. Event Versioning

Events are immutable.

Changes create new versions.

Historical events are never modified.

Backward compatibility SHALL be preserved whenever possible.

---

# 11. Event Ordering

Ordering is guaranteed only within a single aggregate.

Example:

Reservation

↓

Created

↓

Confirmed

↓

Checked In

↓

Checked Out

Ordering across different aggregates is not guaranteed.

---

# 12. Event Consumers

Consumers include:

Analytics

Notification Service

n8n

Search Index

Recommendation Engine

Growth Brain

Monitoring

Audit

Future integrations

Consumers remain independent.

---

# 13. Consumer Responsibilities

Consumers MAY:

Read events.

Generate projections.

Trigger workflows.

Send notifications.

Update search indexes.

Generate analytics.

Consumers SHALL NOT:

Modify transactional entities directly.

---

# 14. Idempotency

Consumers MUST support duplicate delivery.

Every event is uniquely identified.

Repeated processing SHALL produce identical business outcomes.

Duplicate side effects are prohibited.

---

# 15. Failure Handling

Publication failures SHALL NOT invalidate committed business transactions.

Example:

Reservation committed.

↓

Notification failed.

↓

Retry publication.

↓

Reservation remains valid.

Operational consistency has priority over external communication.

---

# 16. Retry Policy

Failed publications remain inside the Outbox.

Background workers retry according to configurable policies.

No committed event is discarded.

---

# 17. Read Models

Read Models are projections generated from events.

Examples:

Revenue Dashboard

Occupancy Calendar

Reservation Statistics

Cleaning Queue

Marketing Metrics

Read Models are disposable.

Operational data is not.

---

# 18. Workflow Integration

Domain Events are the preferred trigger mechanism for asynchronous workflows.

Example:

ReservationConfirmed

↓

Event Published

↓

Workflow Trigger

↓

Guest Notification

↓

Cleaning Assignment

↓

Owner Alert

Workflow ownership follows:

ARCH-0026

---

# 19. Event Sourcing Readiness

ATLAS is Event-Sourcing Ready.

However,

the MVP SHALL NOT implement Event Sourcing.

Operational truth remains inside PostgreSQL.

Historical events enable future migration if justified.

---

# 20. Observability

Every event SHALL expose:

Publication Time

Delivery Time

Retry Count

Consumer Status

Latency

Correlation ID

Organization ID

Observability is mandatory.

---

# 21. Security

Events SHALL enforce:

Tenant isolation.

Authorization boundaries.

Sensitive data protection.

Encryption where applicable.

Consumers receive only authorized information.

---

# 22. Anti-Patterns

Forbidden:

Publishing before commit.

Business logic inside consumers.

Consumers updating transactional tables.

Mutable events.

Multiple Sources of Truth.

Direct workflow-triggered database writes.

Using events as the authoritative operational state.

---

# 23. Success Metrics

The Event-Driven Architecture is considered successful when:

- every committed business transaction generates reliable events;
- no events are lost;
- consumers remain independent;
- projections are recoverable;
- workflows execute asynchronously;
- transactional consistency is preserved.

---

# 24. Future Evolution

Future versions may introduce:

Distributed Event Bus.

Cross-region replication.

Streaming analytics.

CQRS read databases.

External event subscriptions.

Real-time event streaming.

These capabilities extend the architecture without modifying its core principles.

---

# 25. Final Statement

ATLAS adopts an Event-Driven Architecture to decouple transactional operations from asynchronous processing while preserving a single authoritative operational state.

Events communicate completed business facts.

PostgreSQL remains the operational source of truth.

Consumers operate independently through immutable event streams, enabling scalable integrations, workflow automation and analytical capabilities without compromising consistency or business integrity.
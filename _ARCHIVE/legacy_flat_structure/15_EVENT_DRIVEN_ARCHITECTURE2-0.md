# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : DOC-0015
# DOCUMENT    : Event-Driven Architecture
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Core Architecture
#
# REPLACES
#
# 15_EVENT_DRIVEN_ARCHITECTURE.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0005 Workflow Orchestration
# RFC-0010 Tool Invocation & Execution Layer
#
# ARCH-0024 State Consistency Model
# ARCH-0025 MVP Physical Architecture
# ARCH-0026 Workflow Responsibility Model
#
# ============================================================================

# EVENT-DRIVEN ARCHITECTURE

---

# 1. Purpose

This document defines how ATLAS uses Domain Events to coordinate workflows, integrations and asynchronous processing.

Events enable decoupled communication between business components while preserving PostgreSQL as the operational source of truth.

---

# 2. Architectural Principles

ATLAS follows five principles.

## Principle 1

Operational state lives in PostgreSQL.

Events describe state transitions.

---

## Principle 2

Events are immutable.

They are never modified after publication.

---

## Principle 3

Events do not own business state.

They communicate completed business actions.

---

## Principle 4

Events are published only after successful database transactions.

---

## Principle 5

Consumers must tolerate duplicate delivery through idempotent processing.

---

# 3. Event Lifecycle

```
User Action

↓

Application Service

↓

Database Transaction

↓

Commit

↓

Domain Event

↓

Event Dispatcher

↓

Subscribers

↓

Workflow Execution
```

Only committed business actions generate events.

---

# 4. Event Categories

ATLAS defines multiple event domains.

Reservation Events

Guest Events

Payment Events

Task Events

Property Events

Revenue Events

Growth Events

System Events

Each category remains logically independent.

---

# 5. Domain Events

Examples include:

ReservationCreated

ReservationConfirmed

ReservationCancelled

GuestCheckedIn

GuestCheckedOut

PaymentReceived

InvoiceGenerated

TaskAssigned

TaskCompleted

PriceUpdated

CampaignPublished

Events describe facts that already occurred.

---

# 6. Event Metadata

Every event SHALL contain:

Event ID

Event Type

Aggregate ID

Organization ID

Timestamp

Correlation ID

Causation ID

Schema Version

Payload

Metadata guarantees traceability across the platform.

---

# 7. Correlation IDs

Correlation IDs link multiple operations belonging to the same business request.

Example:

WhatsApp Message

↓

Reservation Update

↓

Payment

↓

Confirmation Message

↓

Workflow

↓

Audit

Every component shares the same Correlation ID.

---

# 8. Causation IDs

Causation IDs identify which event produced another event.

Example:

ReservationCreated

↓

PaymentRequested

↓

PaymentReceived

↓

ReservationConfirmed

The complete business chain becomes observable.

---

# 9. Event Publishing

Events are emitted only after successful transaction commit.

This prevents:

- orphan events;
- inconsistent state;
- phantom workflows.

Event publication never occurs before business persistence succeeds.

---

# 10. Event Consumers

Consumers subscribe independently.

Examples:

Notification Service

Workflow Engine

Analytics

Audit

Revenue Intelligence

Growth Intelligence

Future Integrations

Consumers remain loosely coupled.

---
# 11. Event Bus

The Event Dispatcher is responsible for delivering committed Domain Events to interested consumers.

In the MVP architecture:

Application Service

↓

Domain Event

↓

Internal Event Dispatcher

↓

Subscribers

The Event Dispatcher is an internal application component.

An external message broker is not required for the MVP.

---

# 12. Internal vs External Events

ATLAS distinguishes between two event types.

Internal Events

Used inside the platform.

Examples:

ReservationCreated

TaskCompleted

GuestCheckedIn

--------------------------------

External Events

Published to third-party systems.

Examples:

OTA Synchronization

Webhook Notifications

Accounting Integrations

CRM Integrations

Internal events remain implementation details.

External events become integration contracts.

---

# 13. Idempotency

Every consumer SHALL process duplicate events safely.

Typical strategy:

Receive Event

↓

Check Processed Event ID

↓

Already Processed?

↓

Yes → Ignore

↓

No → Execute

↓

Store Event ID

Idempotency guarantees consistent business behavior.

---

# 14. Event Ordering

Ordering is guaranteed only within the same aggregate.

Example:

ReservationCreated

↓

ReservationConfirmed

↓

GuestCheckedIn

↓

GuestCheckedOut

Ordering across unrelated aggregates is not guaranteed.

Consumers must never assume global ordering.

---

# 15. Event Versioning

Events evolve without breaking compatibility.

Every event includes:

Schema Version

Consumers support:

Current Version

↓

Previous Version (when required)

Breaking changes require a new event version.

---

# 16. Retry Strategy

Transient failures trigger automatic retries.

Default strategy:

Immediate Retry

↓

Exponential Backoff

↓

Maximum Attempts

↓

Dead Letter Queue (future)

Retries never violate idempotency.

---

# 17. Workflow Integration

Events initiate asynchronous workflows.

Example:

ReservationConfirmed

↓

Workflow Trigger

↓

Send WhatsApp Confirmation

↓

Schedule Check-in Reminder

↓

Schedule Cleaning Task

↓

Update Analytics

Business logic remains outside workflow definitions.

---

# 18. Revenue Integration

Revenue Intelligence subscribes only to relevant events.

Examples:

ReservationCreated

ReservationCancelled

PriceUpdated

OccupancyChanged

PaymentReceived

Revenue Brain updates occur asynchronously.

---

# 19. Growth Integration

Growth Intelligence subscribes to commercial events.

Examples:

LeadCreated

ConversationStarted

ReservationConfirmed

CampaignPublished

BookingCompleted

These events enrich the Growth Brain without affecting operational transactions.

---

# 20. Audit Integration

Every Domain Event contributes to auditability.

Audit records include:

Event ID

Timestamp

Actor

Affected Aggregate

Previous State (optional)

Current State

Correlation ID

Audit data supports compliance and troubleshooting.

---
# 21. Observability

Every Domain Event generates telemetry.

Recorded information includes:

Event ID

Event Type

Timestamp

Publisher

Consumer

Processing Duration

Retry Count

Correlation ID

Execution Result

These metrics enable end-to-end tracing across the platform.

---

# 22. Security

Events SHALL enforce:

Tenant Isolation

Permission Validation

Payload Validation

Schema Validation

Sensitive Data Protection

Events must never expose confidential information beyond the minimum required for downstream processing.

---

# 23. Event Retention

Operational events are retained according to organizational policies.

Suggested lifecycle:

Hot Storage

↓

Warm Storage

↓

Cold Archive

↓

Deletion (optional)

Retention periods may differ depending on regulatory requirements.

---

# 24. Event Replay

The architecture supports selective replay of Domain Events.

Replay may be used for:

Analytics Rebuild

Workflow Recovery

Projection Rebuild

Debugging

Replay never modifies historical events.

Replay does not recreate business transactions.

---

# 25. Projections

Operational projections provide optimized read models.

Examples:

Reservation Calendar

Occupancy Dashboard

Revenue Dashboard

Task Dashboard

Guest Timeline

Projections are derived from PostgreSQL and Domain Events.

They are disposable and can be rebuilt at any time.

---

# 26. Event Schema

Every Domain Event follows a standardized schema.

Example:

```text
Event ID

Event Type

Aggregate ID

Organization ID

Occurred At

Correlation ID

Causation ID

Version

Payload

Metadata
```

Schema consistency simplifies integrations and debugging.

---

# 27. Failure Handling

Failures during event consumption never invalidate committed business transactions.

Possible actions include:

Retry

↓

Fallback

↓

Compensation Workflow

↓

Manual Intervention

↓

Monitoring Alert

Business consistency always takes priority over workflow completion.

---

# 28. Anti-Patterns

Forbidden:

Publishing events before transaction commit.

Treating events as the operational database.

Embedding business logic inside subscribers.

Cross-tenant event propagation.

Mutable event payloads.

Ignoring idempotency.

Building synchronous workflows on asynchronous assumptions.

Using events as remote procedure calls.

---

# 29. Future Evolution

Future versions may introduce:

Dedicated Event Bus

Kafka

NATS

RabbitMQ

Cloud Pub/Sub

Cross-region event replication

Streaming analytics

CQRS read models

Event sourcing for selected aggregates

The MVP intentionally uses an in-process dispatcher to minimize operational complexity while preserving migration paths.

---

# 30. Final Statement

The Event-Driven Architecture enables ATLAS to coordinate independent components through immutable Domain Events while preserving PostgreSQL as the Single Source of Truth.

By separating operational state from asynchronous communication, the platform achieves loose coupling, auditability, scalability and reliability without introducing unnecessary distributed-system complexity during the MVP phase.
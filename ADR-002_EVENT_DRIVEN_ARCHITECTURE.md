# ADR-002_EVENT_DRIVEN_ARCHITECTURE.md

---
id: ADR-002
title: Adopt Event-Driven Architecture as the Core Communication Pattern
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Architecture
type: Architectural Decision Record
classification: Critical
related:
  - RFC-0001
  - ARCH-003
  - ARCH-005
  - ARCH-006
---

# 1. Context

ATLAS coordinates:

- AI Agents
- Domain Services
- Workflows
- Integrations
- Analytics
- Memory Systems (Brains)

A traditional request/response architecture would create strong coupling between these components.

As the platform evolves, new services and agents must be introduced without modifying existing components.

---

# 2. Decision

ATLAS adopts an **Event-Driven Architecture (EDA)** as its primary communication model.

Business state changes are represented as immutable domain events.

Services communicate primarily through events rather than direct service-to-service calls.

---

# 3. Decision Drivers

The architecture must support:

- loose coupling
- horizontal scalability
- autonomous agents
- asynchronous workflows
- auditability
- replay capability
- future distributed deployment

---

# 4. Alternatives Considered

## Option A — Synchronous REST Calls

Example

```
Reservation Service
↓

Pricing Service
↓

Messaging Service
↓

Analytics Service
```

Rejected.

Problems:

- cascading failures
- tight coupling
- poor scalability
- difficult retries

---

## Option B — Shared Database Communication

Rejected.

Problems:

- hidden dependencies
- race conditions
- unclear ownership
- poor observability

---

## Option C — Event-Driven Architecture

Selected.

Reasons:

- component independence
- replayability
- observability
- extensibility
- AI compatibility

---

# 5. Architectural Model

```
Domain Service

↓

Domain Event

↓

Event Bus

↓

Subscribers

↓

Side Effects
```

Subscribers never know who emitted the event.

Publishers never know who consumes it.

---

# 6. Event Ownership

Every event has exactly one owner.

Examples:

ReservationCreated

Owner:

Reservation Service

---

PaymentConfirmed

Owner:

Payment Service

---

GuestCreated

Owner:

Guest Service

Ownership is never shared.

---

# 7. Event Characteristics

All events are:

- immutable
- append-only
- timestamped
- versioned
- traceable
- tenant-aware

---

# 8. Event Categories

## Domain Events

Business state changes.

Examples:

- ReservationConfirmed
- GuestUpdated
- PriceChanged

---

## AI Events

AI reasoning.

Examples:

- IntentDetected
- AgentSelected
- PlanGenerated

---

## Integration Events

External systems.

Examples:

- WhatsAppMessageReceived
- PaymentWebhookReceived

---

## Infrastructure Events

Operational events.

Examples:

- RetryScheduled
- DeadLetterCreated

---

# 9. Event Lifecycle

```
Business Action

↓

Validation

↓

Database Commit

↓

Domain Event

↓

Event Store

↓

Publication

↓

Subscribers

↓

Side Effects
```

---

# 10. Subscriber Rules

Subscribers must:

- be idempotent
- tolerate duplicate delivery
- never modify received events
- execute independently

Subscribers must NOT:

- assume execution order across aggregates
- perform blocking operations
- create circular event chains

---

# 11. Ordering Guarantees

Ordering is guaranteed only inside an aggregate.

Example:

Reservation

```
Created

↓

Confirmed

↓

CheckedIn

↓

Completed
```

Ordering between different aggregates is not guaranteed.

---

# 12. Event Replay

Replay is a core architectural capability.

Replay supports:

- debugging
- analytics reconstruction
- AI retraining
- memory rebuilding
- disaster recovery

Replay must never modify historical events.

---

# 13. Event Versioning

Events evolve through schema versioning.

Rules:

New optional fields

Compatible.

---

Field removal

Breaking.

Requires new version.

---

Semantic meaning changes

Breaking.

Requires new event type.

---

# 14. Failure Strategy

If subscriber fails:

- retry
- exponential backoff
- dead-letter queue

Publishing must never fail because one subscriber failed.

---

# 15. Event Store

Every event is persisted.

Minimum metadata:

- event_id
- aggregate_id
- aggregate_type
- version
- tenant_id
- timestamp
- payload
- correlation_id
- causation_id

---

# 16. AI Integration

AI Agents subscribe to events.

Examples:

ReservationCreated

↓

Revenue Agent

↓

Suggest dynamic pricing.

---

GuestMessageReceived

↓

Guest Relations Agent

↓

Generate response.

AI never polls databases continuously.

---

# 17. Benefits

The architecture gains:

- loose coupling
- replayability
- observability
- autonomous processing
- easier testing
- simpler integrations
- future distributed execution

---

# 18. Drawbacks

Additional complexity:

- eventual consistency
- event schema management
- monitoring requirements

These trade-offs are accepted.

---

# 19. Anti-Patterns

Forbidden:

- synchronous service chains
- hidden database communication
- mutable events
- events without version
- direct subscriber dependencies
- event payloads containing business logic

---

# 20. Future Evolution

Current MVP:

PostgreSQL event tables.

↓

Future:

Dedicated Event Bus.

↓

Future:

Kafka-compatible streaming.

↓

Future:

Global distributed event mesh.

No application redesign should be required.

---

# 21. Validation Criteria

This ADR is considered correctly implemented when:

- every business state change emits an event;
- services do not depend directly on one another;
- subscribers remain independent;
- replay is possible;
- events are immutable;
- event ownership is explicit.

---

# 22. Status

Accepted.

Event-Driven Architecture becomes the official communication model for every major component of ATLAS.
# 15_EVENT_DRIVEN_ARCHITECTURE.md

---
id: ARCH-0015
title: Event-Driven Architecture (EDA Core)
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Platform Architecture
type: Architecture Specification
classification: Critical
related:
  - RFC-0005
  - RFC-0006
  - ARCH-0011
  - RFC-0008
---

# 1. Purpose

This document defines the **Event-Driven Architecture (EDA)** of ATLAS.

The platform is fundamentally event-first: every meaningful state change is captured as an event, enabling scalability, traceability, AI learning, and system decoupling.

---

# 2. Design Philosophy

ATLAS treats events as the **source of truth for system evolution**.

Core principles:

- everything important is an event
- events are immutable
- events are replayable
- events drive intelligence
- services are reactive, not proactive
- systems are decoupled by default

---

# 3. High-Level Architecture

```
Domain Services
       │
       ▼
Event Producers
       │
       ▼
Event Bus (Core Backbone)
       │
 ┌─────┼───────────────┐
 ▼     ▼               ▼
Growth  Revenue     Memory System
Brain   Brain       (Brains)
       │
       ▼
Workflow Engine (n8n)
       │
       ▼
External Systems
```

---

# 4. Event Categories

## 4.1 Domain Events

Core business events:

- ReservationCreated
- ReservationConfirmed
- ReservationCancelled
- GuestUpdated
- PriceUpdated
- AvailabilityChanged

---

## 4.2 System Events

Infrastructure-level events:

- ToolExecuted
- WorkflowTriggered
- AgentExecuted
- ContextGenerated

---

## 4.3 Integration Events

External communication events:

- WhatsAppMessageSent
- PaymentProcessed
- OTAUpdated
- EmailDelivered

---

# 5. Event Structure

Every event must follow a strict schema:

```json
{
  "event_id": "uuid",
  "event_type": "ReservationConfirmed",
  "timestamp": "ISO-8601",
  "organization_id": "uuid",
  "aggregate_type": "reservation",
  "aggregate_id": "uuid",
  "version": 1,
  "correlation_id": "uuid",
  "payload": {},
  "metadata": {}
}
```

---

# 6. Event Immutability

Events are strictly immutable:

- no updates
- no deletions
- only append operations

Corrections are done via **compensating events**.

Example:

```
PriceUpdated → PriceCorrectionApplied
```

---

# 7. Event Bus Design

The Event Bus is the central backbone:

Responsibilities:

- event routing
- event persistence
- delivery guarantees
- retry logic
- subscription management

Delivery semantics:

- at-least-once delivery
- idempotent consumers required

---

# 8. Event Consumers

Consumers include:

## Memory System
- updates Brain stores
- generates embeddings
- updates context index

## Growth Brain
- attribution tracking
- funnel analysis
- conversion insights

## Revenue Brain
- pricing signals
- demand forecasting

## Workflow Engine
- triggers automation

## Analytics Layer
- aggregates KPIs

---

# 9. Event Processing Pipeline

```
Event Produced
      ↓
Validation
      ↓
Persistence
      ↓
Routing
      ↓
Consumer Delivery
      ↓
Async Processing
      ↓
Side Effects
```

---

# 10. Idempotency Model

All consumers must implement:

- deduplication by event_id
- safe retries
- deterministic processing

Duplicate events must not cause duplicate effects.

---

# 11. Ordering Guarantees

Ordering is guaranteed:

- per aggregate_id
- not globally

Example:

Reservation events remain ordered per reservation.

---

# 12. Event Replay System

Events can be replayed for:

- debugging
- analytics reconstruction
- AI training
- system recovery

Replay pipeline:

```
Event Store → Replay Engine → Consumers
```

---

# 13. Event Store Design

Stored in PostgreSQL:

```
event_store
- event_id
- event_type
- aggregate_id
- payload
- timestamp
- version
```

Partitioned by:

- organization_id
- time ranges

---

# 14. Real-Time vs Batch Processing

## Real-Time
- guest messaging
- pricing updates
- workflow triggers

## Batch
- analytics aggregation
- forecasting
- reporting

---

# 15. AI Integration

Events feed AI systems:

- Memory System updates Brains
- Growth Brain learns from funnels
- Revenue Brain updates forecasts
- Orchestrator uses event context

AI is event-aware, not state-aware.

---

# 16. Event-Driven AI Feedback Loop

```
Event
 ↓
Memory Update
 ↓
AI Context Update
 ↓
Decision Improvement
 ↓
New Event
```

This creates a continuous learning system.

---

# 17. Failure Handling

Failure modes:

## Consumer failure
→ retry with exponential backoff

## Event loss risk
→ persisted event store prevents loss

## Partial failure
→ independent consumer recovery

---

# 18. Security Model

Enforced:

- tenant isolation per event
- signed event validation
- restricted event publishers
- controlled consumer access

---

# 19. Observability

Each event includes tracking:

- processing latency
- consumer success/failure
- retry counts
- downstream impact

Full traceability is mandatory.

---

# 20. Anti-Patterns

Forbidden:

- synchronous business coupling
- event mutation
- silent event dropping
- global ordering assumptions
- non-idempotent consumers
- hidden side effects

---

# 21. Future Evolution

The architecture supports:

- distributed event streaming (Kafka-compatible)
- event sourcing at scale
- real-time streaming analytics
- cross-tenant anonymized insights
- AI-generated event consumers
- predictive event generation

---

# 22. Success Criteria

The Event-Driven Architecture is successful when:

- all meaningful state changes are events
- system components are fully decoupled
- replay is reliable and deterministic
- AI systems learn from event streams
- scalability is achieved without tight coupling

---

# 23. Summary

The Event-Driven Architecture is the nervous system of ATLAS.

It enables real-time responsiveness, AI learning, system decoupling and long-term scalability by ensuring every meaningful change in the platform is captured, distributed and processed as an immutable event.
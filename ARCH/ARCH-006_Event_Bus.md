# 06_EVENT_BUS.md

---
id: ARCH-006
title: Event Bus Architecture
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
---

# 1. Purpose

This document defines the **Event Bus architecture** of ATLAS.

The Event Bus is the central nervous system of the platform.

It enables asynchronous communication between all system components.

---

# 2. Core Principle

> Everything important in ATLAS is an event.

State changes, decisions, workflows, and external interactions must be represented as immutable events.

---

# 3. Event-Driven Architecture Model


Domain Services
↓
Event Bus
↓
Subscribers (Agents / Workflows / Analytics / Memory)
↓
Side Effects


---

# 4. Event Bus Responsibilities

- event ingestion
- event storage
- event routing
- event delivery
- retry mechanisms
- ordering guarantees (per aggregate)
- deduplication
- dead-letter handling

---

# 5. Event Characteristics

All events in ATLAS must be:

## 5.1 Immutable

Once created, events cannot be modified.

---

## 5.2 Append-only

Events are never deleted from the system.

---

## 5.3 Timestamped

Each event includes:

- creation time
- processing time (optional)
- source time (if external)

---

## 5.4 Traceable

Every event must include:

- event_id
- correlation_id
- actor (AI / user / system)
- source service

---

## 5.5 Serializable

Events must be structured and versioned.

---

# 6. Event Structure

Standard event schema:


Event {
id: string
type: string
version: string
timestamp: datetime
correlation_id: string
actor: string
source: string
payload: object
}


---

# 7. Event Categories

## 7.1 Domain Events

Represent business state changes.

Examples:
- ReservationCreated
- ReservationCancelled
- GuestCreated
- PropertyUpdated

---

## 7.2 System Events

Represent infrastructure behavior.

Examples:
- WorkflowTriggered
- ServiceFailure
- RetryScheduled

---

## 7.3 AI Events

Represent AI decisions.

Examples:
- IntentClassified
- AgentSelected
- ResponseGenerated
- ActionRecommended

---

## 7.4 Integration Events

External system interactions.

Examples:
- WhatsAppMessageReceived
- PaymentProcessed
- OTAReservationSynced

---

# 8. Event Flow Lifecycle

Service generates event
Event validated
Event persisted
Event published
Subscribers receive event
Side effects executed

---

# 9. Delivery Guarantees

## 9.1 At-Least-Once Delivery

Events may be delivered multiple times.

Idempotency is required at subscriber level.

---

## 9.2 Ordering Guarantees

Ordering is guaranteed per aggregate:

- Reservation stream ordered
- Guest stream ordered
- Property stream ordered

---

## 9.3 Retry Policy

- exponential backoff
- max retry limit
- dead-letter queue fallback

---

# 10. Subscribers

Event consumers include:

## AI Layer
- Orchestrator Agent
- Domain Agents
- Memory Updaters

## Services Layer
- Analytics Service
- Workflow Service

## External Systems
- n8n
- WhatsApp gateway
- Email services

---

# 11. Event Storage

All events are persisted in PostgreSQL (Supabase).

Stored as:

- event log table
- partitioned by tenant
- indexed by correlation_id and type

---

# 12. Event Replay Capability

System must support:

- full replay of event streams
- partial replay per aggregate
- time-range replay

Used for:

- debugging
- AI reprocessing
- analytics reconstruction

---

# 13. Event Versioning

Events are versioned.

Rules:

- new fields must not break existing consumers
- breaking changes require new event type
- old events remain valid

---

# 14. Failure Modes

## 14.1 Event Not Delivered

- retry queue
- eventual consistency maintained

## 14.2 Subscriber Failure

- isolate failure
- continue event processing
- mark failed delivery

## 14.3 Duplicate Events

- deduplication via event_id

---

# 15. Anti-Patterns

- synchronous service coupling
- hidden state changes without events
- modifying past events
- non-versioned payloads
- skipping event emission for "minor updates"

---

# 16. Security Considerations

- tenant isolation enforced at event level
- sensitive data masked where necessary
- audit log derived from event stream

---

# 17. Performance Requirements

- high-throughput ingestion
- low-latency routing
- partitioned event storage
- scalable consumer groups

---

# 18. Future Extensions

- distributed event streaming (Kafka-compatible layer)
- AI-based event prioritization
- real-time event reasoning engine
- predictive event generation
- cross-tenant anonymized analytics layer

---

# 19. Summary

The Event Bus is the **source of truth for system activity flow**.

It decouples:

- AI decisions
- service execution
- memory updates
- external integrations

This enables ATLAS to scale as an AI-native distributed system without tight coupling between components.
# 07_DATA_FLOW.md

---
id: ARCH-007
title: Data Flow Architecture
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
---

# 1. Purpose

This document defines how data moves through ATLAS.

It describes end-to-end flows between:

- users
- AI layer
- services
- event bus
- persistence layer
- external systems

---

# 2. Core Principle

> Data in ATLAS is event-derived and context-enriched.

There is no single static snapshot of truth outside the event stream and domain model.

---

# 3. Global Data Flow Model


User Input
↓
Conversation Layer
↓
AI Orchestrator
↓
Context Builder (Brains)
↓
Domain Agent
↓
Domain Service
↓
Event Bus
↓
Database
↓
Memory Update
↓
Analytics / Workflows / Side Effects


---

# 4. Input Data Flow

## 4.1 Sources

- WhatsApp messages
- Web dashboard actions
- Email inputs
- External OTA events
- Payment webhooks

---

## 4.2 Normalization

All inputs are transformed into:

- Intent objects
- Structured payloads
- Conversation context packets

Example:


Raw Message:
"¿Hay disponibilidad este finde?"

↓
Intent:
CHECK_AVAILABILITY


---

# 5. AI Context Assembly Flow

Before any decision, the system builds context:

## 5.1 Context Sources

- Guest Brain
- Property Brain
- Growth Brain
- Reservation history
- Pricing history
- Event history
- Conversation history

---

## 5.2 Context Builder Output


AI_Context {
intent
guest_profile
property_state
pricing_state
history_summary
constraints
}


---

# 6. AI Decision Flow


Context
↓
Orchestrator
↓
Agent Selection
↓
Plan Generation
↓
Action Proposal


---

## 6.1 Output Types

Agents can output:

- direct action
- multi-step workflow
- request for clarification
- escalation to human

---

# 7. Execution Flow


Agent Output
↓
Domain Service
↓
Validation
↓
State Change
↓
Event Emission


---

# 8. Event Propagation Flow

Once an event is emitted:


Event Bus
↓
Subscribers
↓
Side Effects


---

## 8.1 Subscribers

- AI Memory Updater
- Analytics Engine
- Workflow Engine (n8n)
- Notification System
- External APIs

---

# 9. Memory Update Flow


Event
↓
Memory Processor
↓
Guest Brain / Property Brain / Growth Brain
↓
Vector + Structured Storage


---

# 10. Response Generation Flow

After execution:


State + Event Result
↓
AI Response Generator
↓
Conversation Layer
↓
User Output (WhatsApp/Web)


---

# 11. External Integration Flow

## 11.1 WhatsApp


WhatsApp API
↓
Message Ingestion
↓
Intent Processing
↓
AI Orchestrator


---

## 11.2 Payment Providers


Payment Webhook
↓
Event Conversion
↓
Event Bus
↓
Reservation Update


---

## 11.3 OTA Platforms (future)


Airbnb / Booking
↓
Sync Layer
↓
Reservation Service
↓
Event Bus


---

# 12. Read vs Write Flow

## Read Path

- AI requests context
- Memory systems queried
- Domain state assembled
- No side effects

---

## Write Path

- AI proposes action
- Service executes
- Event emitted
- Memory updated

---

# 13. Consistency Model

- Strong consistency:
  - reservations
  - payments
  - availability

- Eventual consistency:
  - messaging
  - analytics
  - growth data
  - AI memory updates

---

# 14. Latency Model

Targets:

- WhatsApp response: < 3s
- AI decision: < 2s
- Service execution: < 1s typical
- Event propagation: async

---

# 15. Failure Handling in Data Flow

## AI failure
→ fallback deterministic rule engine

## Service failure
→ retry + queue

## Event failure
→ DLQ (dead letter queue)

## Memory failure
→ degraded context mode

---

# 16. Anti-Patterns

- UI bypassing orchestrator
- direct DB writes from AI
- missing event emission
- synchronous cross-service calls
- stale context usage
- hidden side effects

---

# 17. Security Considerations

- tenant-scoped data flow
- encrypted sensitive payloads
- audit trail per flow step
- restricted context exposure to AI

---

# 18. Observability

Every data flow must produce:

- trace_id
- correlation_id
- event chain
- execution timeline
- AI decision log

---

# 19. Future Extensions

- real-time streaming data pipeline
- predictive flow branching
- autonomous workflow optimization
- cross-property intelligence graph
- MCP execution-aware routing layer

---

# 20. Summary

ATLAS data flow is:

- event-driven
- AI-orchestrated
- memory-enriched
- service-executed

This guarantees traceability, scalability, and full observability across the entire system.
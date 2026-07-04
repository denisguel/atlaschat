# 05_SERVICES.md

---
id: ARCH-005
title: Domain Services
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
---

# 1. Purpose

This document defines the **Domain Services Layer** of ATLAS.

Domain Services are responsible for deterministic execution of business logic.

They sit between:
- AI Agents (decision layer)
- Domain Model (data layer)

---

# 2. Service Design Principles

All services must adhere to:

- Deterministic execution
- No AI logic
- Idempotent operations
- Strict input validation
- Event emission on state change
- Stateless runtime behavior
- Clear contracts (request/response)

---

# 3. Service Layer Position


AI Agents
↓
Domain Services
↓
Domain Model
↓
Event Bus
↓
Database


Agents decide.  
Services execute.  
Domain defines rules.  
Database persists.

---

# 4. Core Services

## 4.1 Reservation Service

### Responsibility
Manages reservation lifecycle.

### Operations
- createReservation
- updateReservation
- cancelReservation
- confirmReservation
- checkIn
- checkOut

### Rules Enforcement
- no overlaps
- valid property state
- valid guest existence

### Events Emitted
- ReservationRequested
- ReservationConfirmed
- ReservationCancelled
- ReservationCheckedIn
- ReservationCheckedOut

---

## 4.2 Property Service

### Responsibility
Manages property data and state.

### Operations
- createProperty
- updateProperty
- deactivateProperty
- activateProperty

### Events
- PropertyCreated
- PropertyUpdated
- PropertyPaused
- PropertyActivated

---

## 4.3 Pricing Service

### Responsibility
Handles pricing logic (deterministic rules only).

### Inputs
- seasonality rules
- occupancy thresholds
- manual overrides
- property constraints

### Outputs
- computed price
- pricing breakdown

### Events
- PriceCalculated
- PriceUpdated

---

## 4.4 Messaging Service

### Responsibility
Handles communication delivery.

### Channels
- WhatsApp
- Email
- Future: SMS / OTA chat

### Operations
- sendMessage
- receiveMessage
- queueMessage

### Events
- MessageSent
- MessageReceived

---

## 4.5 Content Service

### Responsibility
Manages marketing and listing content.

### Operations
- createContent
- updateContent
- publishContent

### Events
- ContentCreated
- ContentPublished

---

## 4.6 Analytics Service

### Responsibility
Aggregates operational and business metrics.

### Inputs
- events
- reservations
- messages
- payments

### Outputs
- occupancy metrics
- revenue metrics
- conversion metrics

### Events
- MetricsUpdated

---

## 4.7 Workflow Service

### Responsibility
Triggers automation flows (n8n integration).

### Triggers
- event-based
- scheduled
- manual trigger (AI or human)

### Events
- WorkflowTriggered
- WorkflowCompleted

---

## 4.8 Auth Service

### Responsibility
Identity and access management.

### Features
- authentication
- authorization
- role management
- tenant isolation

### Events
- UserAuthenticated
- RoleChanged

---

# 5. Service Communication Model

Services NEVER call each other directly.

They communicate via:

## Primary Mechanism
- Event Bus

## Secondary Mechanism
- Request/Response via Orchestrator (only when necessary)

---

# 6. Event Emission Rule

Every service must emit events for:

- state changes
- side effects
- external interactions

Example:


ReservationService.createReservation()
→ writes DB
→ emits ReservationCreated


---

# 7. Idempotency Requirements

All service operations must be safe to retry.

Required:

- idempotency keys
- duplicate detection
- safe reprocessing of events

---

# 8. Error Handling Model

## Types of failures

### 1. Validation Errors
- rejected immediately
- no event emitted

### 2. Business Rule Violations
- rejected with reason
- logged

### 3. Infrastructure Failures
- retried
- queued
- dead-letter fallback

---

# 9. Security Constraints

- tenant isolation enforced at service level
- no cross-tenant data access
- role-based permissions required
- all operations audited

---

# 10. Performance Requirements

- services must be horizontally scalable
- stateless execution required
- database calls minimized
- async processing preferred

---

# 11. Anti-Patterns

- embedding AI logic in services
- direct service-to-service calls
- skipping event emission
- modifying domain state without validation
- storing transient data in services

---

# 12. Service Contracts

All services must expose:

- Input schema
- Output schema
- Event schema
- Error schema

Contracts must be versioned.

---

# 13. Observability Requirements

Each service must produce:

- structured logs
- execution traces
- metrics
- event correlation IDs

---

# 14. Future Extensions

- service mesh architecture
- distributed execution layer
- AI-assisted service optimization
- adaptive pricing services
- predictive workflow services

---

# 15. Summary

Domain Services are the **execution backbone of ATLAS**.

They are deliberately non-intelligent.

They ensure that AI decisions are translated into reliable, deterministic system actions.

This separation is critical to maintain control, predictability and scalability in an AI-native system.
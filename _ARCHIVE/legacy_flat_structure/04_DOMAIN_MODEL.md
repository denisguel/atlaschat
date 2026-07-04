# 04_DOMAIN_MODEL.md

---
id: ARCH-004
title: Domain Model
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
---

# 1. Purpose

This document defines the **core business domain model** of ATLAS.

It describes the real-world entities, relationships, states, and rules that the system must represent.

It is independent of UI, AI implementation, or infrastructure.

---

# 2. Domain Design Principles

- Domain is source of truth for business logic
- Entities are persistent and versioned
- State changes are event-driven
- No logic in UI or presentation layer
- AI operates on domain model, not raw data
- Every entity has lifecycle states

---

# 3. Core Domain Boundaries

ATLAS is structured into 5 main bounded contexts:

Hospitality Operations
Guest Management
Property Management
Revenue Management
Communication & Messaging

Each context is independent but interconnected via events.

---

# 4. Core Entities

## 4.1 Property

Represents a rental unit.

### Attributes

- id
- name
- description
- location
- amenities
- rules
- capacity
- check-in time
- check-out time
- status (active/inactive)

### Lifecycle

- Draft
- Active
- Paused
- Archived

---

## 4.2 Guest

Represents a person interacting with the system.

### Attributes

- id
- name
- phone
- email
- language
- preferences
- score
- tags

### Derived Data

- lifetime value
- cancellation rate
- response behavior
- sentiment score

---

## 4.3 Reservation

Core transactional entity.

### Attributes

- id
- property_id
- guest_id
- check-in date
- check-out date
- status
- price
- currency
- channel

### Status Flow


Requested
→ Confirmed
→ Checked-in
→ Checked-out
→ Completed
→ Cancelled


---

## 4.4 Message

Represents communication events.

### Attributes

- id
- sender
- receiver
- channel
- content
- timestamp
- intent
- status

---

## 4.5 Payment

Represents financial transactions.

### Attributes

- id
- reservation_id
- amount
- currency
- method
- status

---

## 4.6 Workflow

Represents automation processes.

### Attributes

- id
- type
- trigger_event
- status
- execution_log

---

## 4.7 Event

Core system abstraction.

Everything important in ATLAS is an event.

### Attributes

- id
- type
- timestamp
- source
- payload
- actor

---

# 5. Relationships


Guest → Reservation → Property
Reservation → Payment
Reservation → Messages
Property → Workflows
Events → All entities


---

# 6. Domain Rules

## 6.1 Reservation Rules

- A reservation cannot overlap for the same property
- A cancelled reservation cannot be reactivated
- Price must be immutable after confirmation unless explicitly overridden

---

## 6.2 Guest Rules

- One guest can have multiple reservations
- Guest identity is phone-based primary key (normalized via system rules)
- Guest profile is continuously enriched

---

## 6.3 Property Rules

- Property must be active to accept reservations
- Inactive properties cannot generate availability

---

## 6.4 Payment Rules

- Payment must be linked to reservation
- Partial payments allowed only if explicitly enabled

---

# 7. State Machines

## Reservation Lifecycle


Requested
Confirmed
Checked-in
Checked-out
Completed
Cancelled


---

## Property Lifecycle


Draft
Active
Paused
Archived


---

# 8. Domain Events

## Reservation Events

- ReservationRequested
- ReservationConfirmed
- ReservationCancelled
- ReservationCompleted

---

## Guest Events

- GuestCreated
- GuestUpdated
- GuestMessageSent

---

## Property Events

- PropertyCreated
- PropertyUpdated
- PropertyPaused

---

## Financial Events

- PaymentCreated
- PaymentConfirmed
- PaymentFailed

---

# 9. Aggregates

## Reservation Aggregate

Includes:

- Reservation
- Payments
- Messages
- Guest reference
- Property reference

---

## Property Aggregate

Includes:

- Property data
- availability rules
- pricing constraints

---

## Guest Aggregate

Includes:

- Guest profile
- history
- behavior data

---

# 10. Invariants

- No reservation overlaps allowed
- Guest identity must remain consistent
- Events are immutable
- Financial data must be auditable

---

# 11. Consistency Model

- Strong consistency: reservations, payments
- Eventual consistency: messaging, analytics, growth data

---

# 12. Anti-Patterns

- storing guest behavior in AI prompts only
- modifying reservations without events
- bypassing domain rules via services
- duplicating property state across systems

---

# 13. Future Extensions

- dynamic pricing as domain entity
- reputation scoring system
- multi-property guest graph
- predictive booking intent entity
- AI-derived domain objects

---

# 14. Summary

The domain model defines the **real-world structure of ATLAS**.

Everything else (AI, services, UI, workflows) operates on top of this model.

If the domain model is incorrect, the entire system becomes inconsistent.

This is the foundation of the architecture.
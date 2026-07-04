# RFC-0006_HOSPITALITY_DOMAIN_MODEL.md

---
id: RFC-0006
title: Hospitality Domain Model
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Product & Architecture
type: Request For Comments
classification: Critical
related:
  - RFC-0001
  - ARCH-005
  - ADR-002
---

# 1. Purpose

This RFC defines the canonical Hospitality Domain Model of ATLAS.

It establishes the business entities, relationships, ownership boundaries and domain concepts that every service, workflow, AI agent and integration must use.

No implementation may redefine these concepts independently.

---

# 2. Objectives

The Domain Model must:

- provide a ubiquitous language
- separate business domains
- avoid duplicated concepts
- simplify future integrations
- support AI reasoning
- support event-driven architecture
- support multi-tenancy

---

# 3. Core Business Domains

ATLAS is composed of the following domains:

```
Hospitality

├── Organizations
├── Properties
├── Units
├── Guests
├── Reservations
├── Pricing
├── Availability
├── Operations
├── Communications
├── Payments
├── Marketing
├── Analytics
└── Intelligence
```

Each domain owns its own data and business rules.

---

# 4. Organization

Represents the customer using ATLAS.

Examples:

- Independent owner
- Property manager
- Hospitality company

Organization owns:

- properties
- users
- settings
- integrations
- billing

Every resource belongs to exactly one Organization.

---

# 5. Property

A Property represents a hospitality asset.

Examples:

- Apartment
- House
- Cabin
- Hotel
- Hostel
- Boutique Hotel
- Condo
- Resort Villa

Property contains:

- metadata
- amenities
- rules
- address
- media
- availability
- pricing strategy

A Property may contain one or more Units.

---

# 6. Unit

A Unit is the reservable accommodation.

Examples:

- Apartment 3B
- Cabin #4
- Room 210
- Duplex Ocean View

Reservations always reference a Unit.

Properties may expose multiple Units.

---

# 7. Guest

Guest represents a traveler.

Guest owns:

- personal profile
- preferences
- communication history
- reservation history
- loyalty indicators
- satisfaction metrics

Guest identity is global within an Organization.

---

# 8. Reservation

Reservation represents the contractual occupation of a Unit.

Lifecycle:

```
Inquiry

↓

Pending

↓

Confirmed

↓

Checked In

↓

Checked Out

↓

Completed
```

Cancelled reservations remain immutable historical records.

---

# 9. Pricing

Pricing defines the commercial value of availability.

Pricing depends on:

- season
- occupancy
- demand
- promotions
- manual adjustments
- AI recommendations

Pricing history is preserved.

---

# 10. Availability

Availability defines whether a Unit may be booked.

Possible states:

- Available
- Reserved
- Blocked
- Maintenance
- Owner Stay

Availability changes emit domain events.

---

# 11. Operations

Operations include:

- housekeeping
- maintenance
- inspections
- inventory
- check-in preparation
- checkout tasks

Operational tasks are event-driven.

---

# 12. Communications

Communications include:

- WhatsApp
- Email
- Future channels

Every conversation belongs to:

- Guest
- Reservation
- Organization

Messages become part of institutional knowledge.

---

# 13. Payments

Payments represent financial transactions.

Examples:

- deposit
- balance
- refund
- adjustment
- cancellation fee

Payment processing may be delegated to external providers.

Financial history remains immutable.

---

# 14. Marketing

Marketing entities include:

- campaigns
- channels
- content
- attribution
- promotions
- conversion funnels

Marketing intelligence belongs primarily to the Growth Brain.

---

# 15. Analytics

Analytics aggregate operational information.

Examples:

- occupancy
- ADR
- RevPAR
- booking sources
- revenue
- conversion
- guest satisfaction

Analytics never become the operational source of truth.

---

# 16. Intelligence

Intelligence is represented by:

- AI Agents
- Brains
- Memory
- Predictions
- Recommendations

Intelligence augments business operations.

It does not replace business rules.

---

# 17. Domain Relationships

```
Organization

↓

Properties

↓

Units

↓

Reservations

↓

Guests

↓

Communications

↓

Operations

↓

Analytics
```

Relationships remain explicit.

Hidden ownership is forbidden.

---

# 18. Aggregate Ownership

Each aggregate owns its lifecycle.

Examples:

Reservation owns:

- status
- check-in
- checkout

Guest owns:

- profile
- preferences

Property owns:

- amenities
- operational rules

Cross-aggregate mutations require domain events.

---

# 19. Domain Events

Examples:

GuestCreated

ReservationConfirmed

ReservationCancelled

PriceUpdated

AvailabilityBlocked

CheckInCompleted

CleaningAssigned

ReviewReceived

PaymentConfirmed

Every significant business transition emits an event.

---

# 20. AI Interpretation

AI Agents reason using domain concepts.

Example:

Owner:

> "Raise prices next Saturday."

AI translates into:

```
Property

↓

Unit

↓

Availability

↓

Pricing

↓

Revenue Rules

↓

Pricing Tool
```

Natural language never bypasses the domain model.

---

# 21. Multi-Tenant Rules

Every entity belongs to exactly one Organization.

Cross-tenant references are prohibited unless explicitly supported by platform features.

Tenant ownership is enforced at every architectural layer.

---

# 22. Future Evolution

The model supports future domains such as:

- Vendors
- Accounting
- CRM
- Insurance
- Contracts
- Legal
- IoT Devices
- Smart Locks
- Energy Management

New domains must preserve bounded context principles.

---

# 23. Anti-Patterns

Forbidden:

- duplicated entity definitions
- shared ownership
- implicit relationships
- business rules inside UI
- AI-specific entity models
- direct cross-domain database updates

---

# 24. Success Criteria

The Hospitality Domain Model is considered complete when:

- every business concept has a single canonical definition;
- every aggregate has one owner;
- relationships are explicit;
- AI reasons using the same model as business services;
- future domains can be added without modifying existing ones.

---

# 25. Summary

The Hospitality Domain Model defines the common business language of ATLAS.

It ensures that Domain Services, AI Agents, Workflows, APIs and Integrations all operate on the same canonical representation of hospitality operations, enabling consistency, scalability and long-term architectural evolution.
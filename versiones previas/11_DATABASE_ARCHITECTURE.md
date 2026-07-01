# 11_DATABASE_ARCHITECTURE.md

---
id: ARCH-0011
title: Core Database Architecture (PostgreSQL First Design)
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Platform Architecture
type: Architecture Specification
classification: Critical
related:
  - RFC-0006
  - RFC-0003
  - ADR-009
---

# 1. Purpose

This document defines the core database architecture for ATLAS.

The system is designed around **PostgreSQL as the primary system of record**, with event-driven extensibility and future readiness for distributed and hybrid storage systems.

---

# 2. Design Philosophy

ATLAS database architecture follows these principles:

- relational-first (PostgreSQL)
- event-ready (append-only events supported)
- multi-tenant by design
- AI-query optimized
- projection-based read scaling
- strict ownership boundaries
- no shared schema ambiguity

---

# 3. High-Level Architecture

```
        Application Layer
               │
               ▼
     Domain Services Layer
               │
 ┌─────────────┼─────────────┐
 ▼             ▼             ▼
Core DB     Event Store   Read Projections
(Postgres)   (Tables)      (Views / Cache)
```

PostgreSQL is the source of truth for operational state.

Events and projections extend its capabilities.

---

# 4. Core Storage Strategy

## 4.1 Operational Data

Stored in normalized relational tables:

- reservations
- guests
- properties
- units
- organizations
- pricing
- payments

---

## 4.2 Event Store

Append-only tables:

- domain_events
- integration_events
- system_events

Events are immutable.

---

## 4.3 Read Projections

Optimized for AI and UI:

- occupancy_view
- revenue_view
- guest_summary_view
- property_dashboard_view

---

# 5. Multi-Tenancy Model

All tables include:

- organization_id (mandatory)

Isolation is enforced at:

- query layer
- row-level security (RLS)
- service layer validation

---

# 6. Core Schema Design

## 6.1 Organization

```
organizations
- id (PK)
- name
- plan
- created_at
```

---

## 6.2 Properties

```
properties
- id
- organization_id
- name
- location
- metadata (jsonb)
```

---

## 6.3 Units

```
units
- id
- property_id
- name
- capacity
- attributes (jsonb)
```

---

## 6.4 Guests

```
guests
- id
- organization_id
- full_name
- email
- phone
- preferences (jsonb)
```

---

## 6.5 Reservations

```
reservations
- id
- organization_id
- guest_id
- unit_id
- check_in
- check_out
- status
- total_price
- source
```

---

## 6.6 Pricing

```
pricing_rules
- id
- organization_id
- property_id
- unit_id
- rule_type
- value
- conditions (jsonb)
```

---

## 6.7 Payments

```
payments
- id
- reservation_id
- amount
- status
- provider
- transaction_ref
```

---

# 7. Event Store Schema

```
domain_events
- id
- organization_id
- aggregate_type
- aggregate_id
- event_type
- payload (jsonb)
- created_at
- version
- correlation_id
```

Events are immutable and append-only.

---

# 8. Indexing Strategy

Critical indexes:

- organization_id (all tables)
- reservation (unit_id, check_in, check_out)
- guest (email, phone)
- events (aggregate_id, event_type)
- pricing (property_id, unit_id)

Performance is optimized for read-heavy workloads.

---

# 9. JSONB Usage Policy

JSONB is allowed for:

- metadata
- preferences
- flexible attributes
- AI-generated enrichment

JSONB is NOT allowed for:

- core relational logic
- financial calculations
- reservation status
- identity resolution

---

# 10. Row Level Security (RLS)

Every table enforces:

```
organization_id = current_organization()
```

This ensures strict tenant isolation at database level.

---

# 11. Read Scaling Strategy

Reads are optimized using:

- materialized views
- caching layer (future Redis)
- precomputed summaries
- denormalized projections

No business logic exists in views.

---

# 12. Event-Driven Extensions

Database changes emit events:

Example:

```
INSERT INTO reservations

↓

ReservationCreated event
```

Events feed:

- Growth Brain
- Revenue Brain
- Memory System
- AI Orchestrator

---

# 13. AI Optimization Layer

Database is designed for AI consumption:

- structured queries
- semantic-ready views
- embedding-ready fields
- event traceability
- minimal ambiguity schema

---

# 14. Migration Strategy

All schema changes follow:

- versioned migrations
- backward compatibility
- zero-downtime deployment
- event preservation guarantees

---

# 15. Performance Requirements

Targets:

- read queries < 50ms (typical)
- reservation queries < 100ms
- event insert < 20ms
- analytics queries async (materialized)

---

# 16. Backup & Recovery

Strategy:

- continuous WAL archiving
- daily snapshots
- point-in-time recovery
- cross-region backup (future)

---

# 17. Security Model

Enforced:

- RLS policies
- encrypted connections
- restricted service roles
- no direct AI DB access
- audit logging for writes

---

# 18. Anti-Patterns

Forbidden:

- cross-tenant queries
- business logic in SQL procedures
- uncontrolled JSONB usage
- schema-less core entities
- direct AI database access
- duplicated sources of truth

---

# 19. Future Evolution

The architecture supports:

- distributed PostgreSQL clusters
- hybrid OLAP systems
- event store separation (Kafka / EventStoreDB)
- vector database integration
- cross-region scaling
- AI-native query layer

---

# 20. Success Criteria

The database architecture is successful when:

- all core entities are consistently structured
- tenant isolation is guaranteed
- performance remains stable at scale
- event history is fully preserved
- AI systems can query safely and efficiently
- no business logic leaks into persistence layer

---

# 21. Summary

The ATLAS database architecture establishes PostgreSQL as the stable foundation of the platform, while enabling event-driven expansion, AI-native querying, and future scalability without compromising consistency, security, or tenant isolation.
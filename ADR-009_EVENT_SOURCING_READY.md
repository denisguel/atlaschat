# ADR-009_EVENT_SOURCING_READY.md

---
id: ADR-009
title: Design the Platform to be Event Sourcing Ready
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Architecture
type: Architectural Decision Record
classification: Strategic
related:
  - ARCH-004
  - ARCH-006
  - ADR-002
---

# 1. Context

ATLAS already adopts an Event-Driven Architecture.

However, an event-driven system is not necessarily an Event Sourcing system.

The platform must preserve the option of evolving toward Event Sourcing in the future without requiring fundamental architectural changes.

---

# 2. Decision

ATLAS will initially use a traditional relational state model backed by PostgreSQL.

At the same time, every important business state transition will emit immutable domain events.

The architecture must remain compatible with future Event Sourcing adoption.

---

# 3. Decision Drivers

Primary motivations:

- complete auditability
- historical reconstruction
- AI learning from history
- replay capability
- debugging
- regulatory compliance
- future scalability

---

# 4. Current Architecture

Current MVP

```
Business Command

↓

Domain Service

↓

Database State

↓

Domain Event
```

State remains the operational source.

Events become historical truth.

---

# 5. Future Architecture

Possible future evolution

```
Business Command

↓

Domain Event

↓

Event Store

↓

Materialized Views

↓

Queries
```

No domain redesign should be required.

---

# 6. Event Principles

Events must always be:

- immutable
- timestamped
- versioned
- append-only
- traceable

Events are facts.

They never change.

---

# 7. State Ownership

Current ownership:

Operational State

↓

PostgreSQL

Historical State

↓

Event Store

Future versions may reconstruct operational state entirely from events.

---

# 8. Aggregate Streams

Events belong to aggregates.

Examples:

Reservation Stream

```
Requested

↓

Confirmed

↓

Checked In

↓

Checked Out

↓

Completed
```

Guest Stream

```
Created

↓

Preference Updated

↓

Reservation Added

↓

Review Received
```

Each aggregate owns its own ordered history.

---

# 9. Replay Capability

The platform should support replay of:

- individual aggregates
- tenants
- date ranges
- complete systems

Replay is primarily intended for:

- debugging
- AI memory rebuilding
- analytics regeneration
- migration

---

# 10. Materialized Views

Operational tables are considered materialized projections.

Examples:

Reservation Table

↓

Projection

Guest Dashboard

↓

Projection

Revenue Report

↓

Projection

Future projections may be rebuilt from events.

---

# 11. Event Store

Initial implementation:

PostgreSQL event tables.

Future implementations:

- Kafka
- EventStoreDB
- cloud-native event platforms

Application architecture remains unchanged.

---

# 12. AI Benefits

Historical events become valuable training signals.

Examples:

Reservation lifecycle

↓

Revenue optimization.

Guest behavior

↓

Personalization.

Marketing events

↓

Conversion prediction.

Events become long-term intelligence.

---

# 13. Storage Strategy

Operational state:

Optimized for reads.

Historical events:

Optimized for append operations.

These concerns remain independent.

---

# 14. Performance

The operational system reads projections.

It does not reconstruct state from events on every request.

Future migration remains optional.

---

# 15. Security

Events inherit:

- tenant ownership
- authorization rules
- audit requirements

Historical events remain immutable.

---

# 16. Observability

Each event records:

- aggregate_id
- aggregate_type
- version
- timestamp
- actor
- correlation_id
- causation_id
- payload

This enables complete execution tracing.

---

# 17. Benefits

Preparing for Event Sourcing enables:

- perfect audit history
- deterministic replay
- historical AI reasoning
- simplified debugging
- richer analytics
- regulatory support

---

# 18. Trade-Offs

Accepted costs:

- additional storage
- schema versioning
- event management
- projection maintenance

These are acceptable because they avoid future architectural rewrites.

---

# 19. Alternatives Rejected

## State Only

Rejected.

Reasons:

- limited history
- difficult debugging
- weak explainability

---

## Immediate Full Event Sourcing

Rejected for MVP.

Reasons:

- unnecessary complexity
- slower development
- higher operational burden

The architecture remains prepared instead.

---

# 20. Migration Strategy

Current

State

+

Events

↓

Future

Event Store

↓

Materialized Views

↓

Fully Event Sourced Platform

Migration should occur incrementally.

---

# 21. Success Criteria

This ADR is considered successful when:

- every important state transition emits an immutable event;
- historical replay is possible;
- projections can be rebuilt;
- no future Event Sourcing migration requires domain redesign.

---

# 22. Status

Accepted.

ATLAS officially adopts an Event Sourcing Ready architecture.

Although the MVP uses PostgreSQL as the operational source of truth, the platform preserves a clean migration path toward full Event Sourcing by treating immutable business events as first-class architectural citizens.
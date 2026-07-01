# 10_SCALABILITY.md

---
id: ARCH-010
title: Scalability & Performance Architecture
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
classification: Critical
---

# 1. Purpose

This document defines how ATLAS scales from a single-property owner to a multi-country AI-native hospitality platform.

Scalability is not an optimization phase.

It is an architectural property designed from day one.

---

# 2. Scalability Principles

ATLAS must scale independently across:

- Users
- Organizations
- Properties
- Reservations
- Messages
- AI Requests
- Workflows
- Events
- Storage
- Integrations

No component should become a single point of failure.

---

# 3. Horizontal First

Whenever possible, the platform must scale horizontally instead of vertically.

Preferred strategy:

```
More Nodes

instead of

Bigger Nodes
```

---

# 4. Scalability Dimensions

## User Scale

Growth of authenticated users.

Target:

Millions of users.

---

## Organization Scale

Growth of companies using ATLAS.

Target:

Unlimited organizations.

---

## Property Scale

Growth of managed properties.

Target:

Millions of active properties.

---

## Reservation Scale

Target:

Tens of millions of reservations.

---

## Messaging Scale

Target:

Hundreds of millions of messages.

---

## Event Scale

Target:

Billions of events.

---

## AI Scale

Target:

Millions of AI requests/day.

---

# 5. Layer-by-Layer Scalability

## Conversation Layer

Stateless.

Can scale infinitely behind load balancers.

---

## AI Layer

Independent scaling.

Each agent executes independently.

Future:

Dedicated inference clusters.

---

## Domain Services

Stateless.

Can be replicated horizontally.

---

## Event Bus

Must support:

- partitioning
- consumer groups
- asynchronous processing

Future migration path:

Kafka-compatible architecture.

---

## Database

Initial:

Supabase PostgreSQL.

Future:

Read replicas.

Partitioning.

Sharding (only if necessary).

---

## Storage

Object storage.

Unlimited expansion.

Independent of compute layer.

---

# 6. AI Scalability

AI requests are expensive.

Strategies:

- context minimization
- caching
- routing optimization
- model selection
- batching

Future:

Small model → large model escalation.

---

# 7. Context Retrieval Scaling

Brains must support:

- vector search
- indexed retrieval
- semantic compression
- incremental memory loading

Avoid loading full histories.

---

# 8. Event Processing Scaling

Consumers operate independently.

```
Event

↓

Consumer A

Consumer B

Consumer C

Consumer D
```

Consumers must never block each other.

---

# 9. Workflow Scaling

Every workflow executes independently.

Long-running workflows must be asynchronous.

Workflow failures must never impact core services.

---

# 10. API Scaling

API Gateway:

- stateless
- horizontally scalable
- distributed rate limiting

---

# 11. Database Scaling Strategy

Phase 1

Single PostgreSQL instance.

---

Phase 2

Read replicas.

---

Phase 3

Partitioned tables.

---

Phase 4

Selective service decomposition.

---

Phase 5

Geo-distributed architecture.

---

# 12. Memory Scaling

Brains evolve independently.

Guest Brain

↓

Property Brain

↓

Growth Brain

↓

Future Specialized Brains

Each brain may adopt different storage technologies.

---

# 13. Multi-Agent Scaling

Agents execute independently.

```
Orchestrator

↓

Revenue

Guest

Operations

Content

Conversion
```

Future:

Dynamic agent spawning.

---

# 14. Multi-Tenant Scaling

Every tenant is logically isolated.

Future:

Dedicated enterprise deployments.

Dedicated databases if required.

---

# 15. Geographic Scaling

Initial:

Single region.

Future:

Multi-region.

Eventually:

Edge deployments.

---

# 16. Caching Strategy

Levels:

L1

Application Cache

---

L2

Distributed Cache

---

L3

CDN

---

L4

AI Response Cache

Cache invalidation is event-driven.

---

# 17. Queue Strategy

Long operations:

- AI
- notifications
- integrations
- analytics
- reports

must execute asynchronously.

---

# 18. Performance Targets

Dashboard:

< 2 seconds

---

WhatsApp response:

< 3 seconds

---

Reservation creation:

< 1 second

---

Availability lookup:

< 500 ms

---

Property loading:

< 300 ms

---

# 19. Failure Isolation

Failures must remain local.

Examples:

Messaging failure

↓

Messaging degraded

↓

Reservations continue operating.

Never cascade failures.

---

# 20. Circuit Breakers

Required for:

- AI providers
- payment providers
- OTA integrations
- messaging providers

---

# 21. Graceful Degradation

If AI becomes unavailable:

System switches to:

- deterministic rules
- manual workflows
- cached knowledge

Business operations continue.

---

# 22. Observability

Every layer must expose:

- metrics
- logs
- traces
- health checks

Scalability without observability is unacceptable.

---

# 23. Cost Scalability

Growth in customers must not produce proportional infrastructure costs.

Priority:

High gross margin.

AI costs must be continuously optimized.

---

# 24. Capacity Planning

Architecture should sustain at minimum:

- 100,000 organizations
- 2,000,000 properties
- 100,000,000 reservations
- 1,000,000,000 events
- 10,000,000 AI conversations/day

Without architectural redesign.

---

# 25. Future Evolution

Future improvements include:

- Kubernetes deployment
- Service Mesh
- Distributed Event Streaming
- Multi-region replication
- Edge AI inference
- MCP-native execution fabric

---

# 26. Definition of Done

The scalability architecture is complete when:

- every component scales independently;
- no layer is a bottleneck by design;
- failures are isolated;
- AI costs remain predictable;
- infrastructure can evolve without application redesign.

---

# 27. Summary

ATLAS is designed to evolve from an MVP running on Supabase and Vercel into a globally distributed AI-native hospitality operating system.

Scalability is achieved through:

- stateless services
- event-driven communication
- independent AI agents
- modular bounded contexts
- tenant isolation
- horizontal expansion

The architecture must allow growth in volume, complexity and intelligence without requiring fundamental redesign.
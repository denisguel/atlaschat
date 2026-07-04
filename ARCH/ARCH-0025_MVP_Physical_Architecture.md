# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0025
# DOCUMENT    : MVP Physical Architecture
# VERSION     : 1.0
# STATUS      : ACCEPTED
# CLASS       : Core Architecture
#
# NOTE
#
# This document is NEW.
#
# It does NOT replace any previous specification.
#
# This specification defines the difference between the logical architecture
# and the physical architecture of the MVP.
#
# Future versions of:
#
# - RFC-0008
# - ARCH-0012
# - ARCH-0014
# - ARCH-0018
# - ARCH-0020
#
# MUST reference this document.
#
# ============================================================================

# ARCH-0025

# MVP PHYSICAL ARCHITECTURE

---

# 1. Purpose

This document defines the physical deployment architecture of the ATLAS MVP.

Its objective is to distinguish between:

- Logical Architecture
- Physical Architecture

The logical architecture defines responsibilities.

The physical architecture defines deployment.

These concepts SHALL remain independent.

---

# 2. Motivation

One of the most common architectural mistakes is assuming that every logical component must become an independent microservice.

This assumption increases:

- infrastructure cost
- deployment complexity
- latency
- operational overhead
- debugging difficulty

For an MVP, these disadvantages outweigh any potential benefit.

---

# 3. Architectural Principle

Logical separation DOES NOT imply physical separation.

Every logical module must exist.

Not every logical module must be deployed independently.

---

# 4. Logical vs Physical

Logical components describe responsibilities.

Examples:

- Guest Agent
- Revenue Agent
- Planner
- Context Builder
- Decision Validator

These are architectural concepts.

They are not deployment units.

---

# 5. MVP Deployment Philosophy

The MVP SHALL prioritize:

- simplicity
- maintainability
- low latency
- low infrastructure cost
- rapid iteration

Future scalability must not compromise MVP execution speed.

---

# 6. Official MVP Architecture

```
                Users

                   │

          WhatsApp / Web

                   │

             Next.js Frontend

                   │

          API / Backend (Monolith)

                   │

     ---------------------------------

     Orchestrator

     Planner

     Context Builder

     Decision Validator

     Tool Dispatcher

     Domain Services

     AI Agents

     ---------------------------------

                   │

              PostgreSQL

              pgvector

              Storage

                   │

                 n8n

          (async automation)

                   │

         External Integrations
```

Every core component executes inside the same backend process.

---

# 7. Monolith First

ATLAS adopts a Modular Monolith architecture.

Advantages:

Single deployment.

Single repository.

Single transaction boundary.

Simple debugging.

Simple monitoring.

Fast development.

---

# 8. Internal Modules

Modules remain isolated by responsibility.

Examples:

Reservation Module

Pricing Module

Cleaning Module

Guest Module

Messaging Module

Revenue Module

AI Module

Each module owns its business logic.

---

# 9. AI Components

Logical AI components include:

Orchestrator

Planner

Context Builder

Agent Router

Guest Agent

Revenue Agent

Operations Agent

Content Agent

Decision Validator

These components communicate through in-process interfaces.

Network communication is unnecessary.

---

# 10. Communication

During the MVP:

Function calls.

Not HTTP.

Not gRPC.

Not queues.

Internal communication minimizes latency.

---

# 11. Database

A single PostgreSQL cluster stores:

Operational data

Vectors

Metadata

Audit logs

Outbox

No database fragmentation.

---

# 12. AI Models

One provider.

Multiple prompts.

Shared infrastructure.

Future support for multiple providers is abstracted behind the AI Adapter.

---

# 13. Workflow Engine

n8n is external to the transactional core.

Responsibilities:

notifications

scheduled reminders

OTA synchronization

background workflows

No business rules.

No transactional decisions.

---

# 14. External Systems

External services include:

WhatsApp

Stripe

Mercado Pago

Booking.com

Airbnb

Email

Maps

Weather

Smart Locks

All integrations communicate through adapters.

---

# 15. Domain Services

Every write operation passes through Domain Services.

No external component writes directly into PostgreSQL.

---

# 16. Scalability Strategy

Scaling order:

Vertical

↓

Read optimization

↓

Caching

↓

Background workers

↓

Horizontal scaling

↓

Microservices

Premature decomposition is prohibited.

---

# 17. Future Extraction

The following modules are candidates for future extraction:

Messaging

Revenue Engine

Search

Recommendation Engine

Notification Service

Analytics

Extraction occurs only when justified by measurable bottlenecks.

---

# 18. Infrastructure Cost

The MVP is designed to run on minimal infrastructure.

Example:

One application server.

One PostgreSQL instance.

One Redis instance (optional).

One n8n instance.

This minimizes operating cost.

---

# 19. Deployment

Reference deployment:

Frontend

↓

Next.js

Backend

↓

Node.js

Database

↓

Supabase PostgreSQL

Automation

↓

n8n

Monitoring

↓

OpenTelemetry

Hosting remains cloud-agnostic.

---

# 20. Security

Internal modules trust only authenticated requests.

External traffic passes through:

Authentication

Authorization

Rate Limiting

Decision Validation

Business Rules

Only then may execution occur.

---

# 21. Observability

Every request receives:

Request ID

Correlation ID

Trace ID

Organization ID

User ID

These identifiers propagate across the entire execution chain.

---

# 22. Anti-Patterns

Forbidden:

Microservices without measurable need.

Independent databases per module.

Network communication between local modules.

Duplicated business logic.

Multiple orchestrators.

AI components deployed independently during MVP.

---

# 23. Evolution Path

Phase 1

Modular Monolith

↓

Phase 2

Selective Background Workers

↓

Phase 3

Service Extraction

↓

Phase 4

Distributed Architecture

Each phase requires measurable justification.

---

# 24. Success Metrics

The MVP architecture is considered successful when:

- deployment remains simple;
- latency stays below target;
- infrastructure costs remain predictable;
- modules remain loosely coupled;
- migration paths remain available;
- no premature complexity is introduced.

---

# 25. Final Statement

ATLAS separates logical architecture from physical deployment.

The platform is architected as a rich set of logical components operating within a single modular backend.

This approach preserves architectural clarity while minimizing infrastructure complexity, operational cost and latency during the MVP, without sacrificing future scalability.
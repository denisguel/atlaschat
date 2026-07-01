```markdown
# ADR-001_SUPABASE_AS_FOUNDATION.md

---
id: ADR-001
title: Adopt Supabase as the Primary Backend Platform
status: Accepted
date: 2026-06-29
deciders:
  - Architecture
type: Architectural Decision Record
supersedes: none
superseded_by: none
related:
  - RFC-0001
  - ARCH-002
  - ARCH-005
---

# 1. Context

ATLAS requires a backend platform capable of supporting:

- rapid MVP development
- AI-native architecture
- multi-tenancy
- authentication
- relational database
- object storage
- realtime capabilities
- serverless execution
- low operational cost

The platform must also allow migration toward a distributed architecture without requiring a complete rewrite.

---

# 2. Decision

ATLAS adopts **Supabase** as the foundational backend platform.

Supabase will initially provide:

- PostgreSQL
- Authentication
- Row Level Security
- Object Storage
- Edge Functions
- Realtime
- Database Functions
- Database Triggers

---

# 3. Decision Drivers

Primary drivers:

- MVP development speed
- PostgreSQL compatibility
- predictable pricing
- open-source ecosystem
- SQL-first philosophy
- vendor transparency
- self-hosting option
- excellent developer experience

---

# 4. Benefits

## Development Speed

Authentication, database, storage and API are available immediately.

---

## PostgreSQL

Avoids proprietary databases.

Allows:

- SQL
- Views
- Functions
- Triggers
- Extensions

---

## AI Compatibility

PostgreSQL integrates naturally with:

- pgvector
- embeddings
- semantic search
- retrieval pipelines

---

## Security

Native:

- JWT
- Row Level Security
- Storage Policies

---

## Scalability

Supports migration toward:

- read replicas
- dedicated infrastructure
- external services

---

# 5. Risks

## Vendor Dependency

Although Supabase is open source, hosted services create operational dependency.

Mitigation:

Application logic remains outside Supabase.

---

## Edge Function Limits

Heavy AI processing should not execute inside Edge Functions.

Mitigation:

AI orchestration remains independent.

---

## Realtime Scaling

Large-scale event streaming may eventually exceed Realtime capabilities.

Mitigation:

Future migration toward dedicated event infrastructure.

---

# 6. Alternatives Considered

## Firebase

Rejected.

Reasons:

- NoSQL-first
- vendor lock-in
- weaker relational capabilities
- SQL not available

---

## MongoDB Atlas

Rejected.

Reasons:

- document model unsuitable for reservations
- relational consistency harder
- event-driven joins become complex

---

## Self-hosted PostgreSQL

Rejected for MVP.

Reasons:

- infrastructure burden
- slower iteration
- operational overhead

---

## AWS Native Stack

Rejected.

Components:

- RDS
- Cognito
- S3
- Lambda

Reasons:

- excessive complexity
- slower development
- higher operational cost

---

# 7. Architectural Consequences

Positive:

- rapid MVP
- SQL-first
- strong security
- easy local development

Negative:

- dependency on Supabase APIs
- future migration planning required

---

# 8. Implementation Rules

Supabase owns:

- authentication
- persistence
- storage
- realtime
- edge functions

Supabase does NOT own:

- AI
- orchestration
- workflows
- business logic
- agent execution

---

# 9. Migration Strategy

Future architecture must allow gradual extraction.

Possible order:

Phase 1

Supabase only.

↓

Phase 2

External AI services.

↓

Phase 3

Dedicated Event Bus.

↓

Phase 4

Dedicated Workflow Engine.

↓

Phase 5

Dedicated Microservices.

↓

Phase 6

Distributed Architecture.

No breaking rewrite should be required.

---

# 10. Validation Criteria

This ADR remains valid while Supabase satisfies:

- performance targets
- pricing targets
- scalability targets
- reliability targets
- PostgreSQL compatibility

---

# 11. Review Trigger

This decision must be reviewed if:

- AI traffic increases by 100x
- database throughput exceeds PostgreSQL limits
- infrastructure costs become disproportionate
- enterprise customers require dedicated deployments

---

# 12. Related Documents

RFC-0001 — AI Native Architecture

ARCH-002 — High Level Architecture

ARCH-005 — Domain Services

DATABASE specifications

Infrastructure specifications

---

# 13. Status

Accepted.

This ADR establishes Supabase as the foundational backend platform for the ATLAS MVP while preserving a migration path toward a fully distributed AI-native architecture.
```

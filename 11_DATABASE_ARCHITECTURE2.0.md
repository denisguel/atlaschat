# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : DOC-0011
# DOCUMENT    : Database Architecture
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Core Architecture
#
# REPLACES
#
# 11_DATABASE_ARCHITECTURE.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0005 Workflow Orchestration
# RFC-0010 Tool Invocation & Execution Layer
#
# ARCH-0024 State Consistency Model
# ARCH-0025 MVP Physical Architecture
# ARCH-0029 Context Retrieval Strategy
#
# ============================================================================

# DATABASE ARCHITECTURE

---

# 1. Purpose

This document defines the persistent data architecture of ATLAS.

The database is responsible for storing deterministic operational state.

Artificial Intelligence never becomes the source of truth.

Business state always resides inside PostgreSQL.

---

# 2. Architectural Principles

The database follows five immutable principles.

## Principle 1

PostgreSQL is the Single Source of Truth (SSOT).

---

## Principle 2

Operational state and semantic memory are different systems.

---

## Principle 3

Business transactions must be ACID compliant.

---

## Principle 4

Every change must be auditable.

---

## Principle 5

Database design prioritizes consistency over premature optimization.

---

# 3. Technology Stack

Primary Database

PostgreSQL (Supabase)

Extensions

- pgvector
- PostGIS (future)
- pg_cron
- pgjwt

Storage

Supabase Storage

Authentication

Supabase Auth

Realtime

Supabase Realtime

---

# 4. Database Responsibilities

The database stores:

Organizations

Properties

Units

Guests

Reservations

Owners

Users

Payments

Invoices

Tasks

Conversations

Operational Events

Configuration

Permissions

Audit Logs

It does not store AI reasoning.

---

# 5. Data Categories

ATLAS separates information into four categories.

Operational Data

↓

Configuration Data

↓

Analytical Data

↓

Semantic Knowledge

Only the first three belong in PostgreSQL.

Semantic knowledge is retrieved through the Memory Architecture.

---

# 6. Multi-Tenant Model

Every business object belongs to exactly one Organization.

Organization

↓

Property

↓

Unit

↓

Reservation

↓

Guest

↓

Conversation

↓

Payment

Tenant isolation is mandatory.

---

# 7. Entity Relationships

Core hierarchy:

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

Payments

↓

Tasks

↓

Messages

Every relationship enforces referential integrity.

---

# 8. Normalization

Operational tables follow Third Normal Form where appropriate.

Denormalization is permitted only when justified by measurable performance requirements.

Every denormalized field must identify its authoritative source.

---

# 9. Identifiers

Primary keys use UUIDs.

Advantages:

- globally unique;
- API friendly;
- replication safe;
- merge safe.

Sequential numeric identifiers are avoided for business entities.

---

# 10. Timestamp Policy

Every mutable table includes:

created_at

updated_at

Optional fields:

deleted_at

archived_at

executed_at

completed_at

Timestamps use UTC.

---
# 11. Transaction Model

Every business operation executes inside a database transaction.

Examples:

Reservation Creation

↓

Availability Validation

↓

Payment Registration

↓

Reservation Persistence

↓

Domain Event Registration

↓

Commit

If any step fails, the transaction is rolled back.

Partial business state is never persisted.

---

# 12. State Management

ATLAS persists only the current operational state.

Historical state changes are recorded through immutable Domain Events.

Operational queries always read from PostgreSQL.

Historical reconstruction uses the Event Log when necessary.

This follows the State Consistency Model (ARCH-0024).

---

# 13. Domain Events

Domain Events are immutable records describing completed business actions.

Examples:

ReservationCreated

ReservationConfirmed

ReservationCancelled

GuestCheckedIn

GuestCheckedOut

PaymentReceived

TaskCompleted

Events support:

- auditability;
- integrations;
- workflow triggering;
- analytics.

Events do not replace the operational database.

---

# 14. Row-Level Security (RLS)

Every table SHALL implement Row-Level Security.

Policies enforce:

Organization isolation

↓

Property isolation

↓

User permissions

↓

Role permissions

↓

Data visibility

No query may access another tenant's operational data.

---

# 15. Indexing Strategy

Indexes prioritize operational performance.

Typical indexes include:

Primary Keys

Foreign Keys

Reservation Dates

Property ID

Organization ID

Guest ID

Status

Created Date

Composite indexes are introduced only after measuring query performance.

---

# 16. Soft Delete Policy

Business entities are rarely physically deleted.

Preferred lifecycle:

Active

↓

Archived

↓

Soft Deleted

↓

Retention Expired

↓

Physical Removal (optional)

This preserves historical consistency.

---

# 17. Audit Model

Critical business operations generate audit records.

Examples:

Price Changes

Reservation Changes

Permission Changes

Configuration Updates

Payment Actions

Audit records include:

Actor

Timestamp

Previous Value

New Value

Correlation ID

Reason (when applicable)

---

# 18. Concurrency Control

Concurrent updates are managed using optimistic concurrency.

Typical workflow:

Read Record

↓

Validate Version

↓

Apply Changes

↓

Commit

↓

Conflict Detection

Conflicting updates return explicit errors rather than silently overwriting data.

---

# 19. Data Integrity

Integrity constraints include:

Foreign Keys

Unique Constraints

Check Constraints

Business Constraints

Application validation complements database validation but never replaces it.

---

# 20. Performance Principles

The operational database is optimized for:

Low-latency writes

Fast transactional reads

High consistency

Predictable performance

Analytical workloads should not impact transactional performance.

Analytical processing may use replicated or derived datasets in future versions.

---
# 21. Partitioning Strategy

Large operational tables may be partitioned when justified by measurable workload.

Candidate tables include:

Reservations

Messages

Audit Logs

Domain Events

Analytics

Partitioning shall remain transparent to the application layer.

---

# 22. Vector Storage

Semantic embeddings are stored separately from operational entities.

ATLAS uses pgvector for semantic retrieval.

Typical embedded objects include:

Guest Preferences

Property Knowledge

Conversation Summaries

Documentation

Maintenance Notes

Vector search never replaces relational queries.

---

# 23. Full-Text Search

Operational search supports:

Guest Names

Reservation Codes

Property Names

Messages

Documentation

Full-text indexes complement semantic search.

Each mechanism serves a different purpose.

---

# 24. Backup Strategy

Database backups SHALL include:

Daily Full Backup

↓

Point-in-Time Recovery

↓

Transaction Logs

↓

Periodic Restore Validation

Backup integrity is verified through restoration testing.

---

# 25. Disaster Recovery

Recovery objectives:

Recovery Point Objective (RPO)

≤ 5 minutes

Recovery Time Objective (RTO)

≤ 30 minutes

Operational procedures shall be documented and periodically tested.

---

# 26. Encryption

Sensitive information SHALL be protected using:

Encryption at Rest

Encryption in Transit

Secret Management

Secure Credential Storage

Personally identifiable information follows applicable privacy regulations.

---

# 27. Observability

Database telemetry includes:

Query Latency

Slow Queries

Connection Pool Usage

Transaction Duration

Deadlocks

Replication Status

Storage Growth

Index Usage

Observability supports proactive optimization.

---

# 28. Migration Strategy

Database schema changes SHALL be version controlled.

Migration lifecycle:

Development

↓

Review

↓

Testing

↓

Deployment

↓

Verification

↓

Rollback Capability

Schema evolution must never require manual production changes.

---

# 29. Anti-Patterns

Forbidden:

Business Logic inside SQL.

AI modifying database tables directly.

Cross-tenant queries.

Duplicated operational state.

Missing foreign keys.

Unbounded JSON storage.

Using PostgreSQL as a message broker.

Embedding every table into vectors.

Premature denormalization.

---

# 30. Future Evolution

Future versions may introduce:

Read Replicas

Regional Replication

Sharding

Columnar Analytics

Time-Series Optimization

Graph Extensions

Dedicated Analytics Warehouse

Distributed Event Storage

Future evolution must preserve PostgreSQL as the operational source of truth.

---

# 31. Final Statement

The Database Architecture provides the deterministic operational foundation of ATLAS.

By separating transactional state, semantic knowledge, analytical information and event history into clearly defined responsibilities, the platform achieves consistency, auditability, scalability and maintainability.

PostgreSQL remains the Single Source of Truth, while every AI component, workflow and external integration relies on deterministic data managed through this architecture.
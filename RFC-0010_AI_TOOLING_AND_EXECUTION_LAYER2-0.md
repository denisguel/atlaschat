# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : RFC-0010
# DOCUMENT    : Tool Invocation & Execution Layer
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Core Architecture
#
# REPLACES
#
# RFC-0010_AI_TOOLING_LAYER.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0002 AI Orchestrator
#
# ARCH-0021 AI Execution Levels
# ARCH-0022 Business Decision Validation Layer
# ARCH-0024 State Consistency Model
# ARCH-0025 MVP Physical Architecture
# ARCH-0026 Workflow Responsibility Model
#
# ============================================================================

# RFC-0010

# TOOL INVOCATION & EXECUTION LAYER

---

# 1. Purpose

This document defines how Artificial Intelligence interacts with deterministic software inside ATLAS.

It establishes the architectural boundary between reasoning and execution, ensuring that AI can propose actions while only trusted application services are allowed to modify business state.

---

# 2. Motivation

Large Language Models are probabilistic systems.

Business operations require deterministic execution.

ATLAS therefore separates:

Understanding

↓

Validation

↓

Execution

This separation guarantees safety, auditability and predictable behavior.

---

# 3. Core Principles

The following principles are immutable.

### Principle 1

AI never executes business operations.

---

### Principle 2

AI never accesses PostgreSQL directly.

---

### Principle 3

Every business operation requires validation before execution.

---

### Principle 4

Every execution is deterministic.

---

### Principle 5

Every execution is observable.

---

# 4. Execution Pipeline

```
User Request

↓

AI Orchestrator

↓

Decision Proposal

↓

Business Decision Validation Layer

↓

Tool Dispatcher

↓

Application Service

↓

Domain Service

↓

PostgreSQL

↓

Domain Events
```

---

# 5. Layer Responsibilities

## AI

Responsible for:

- understanding
- extraction
- classification
- planning
- recommendation

Never executes.

---

## Business Decision Validation Layer

Responsible for:

- semantic validation
- policy validation
- approval workflows
- risk analysis
- execution authorization

Never executes.

---

## Tool Dispatcher

Responsible for:

- tool discovery
- parameter validation
- schema validation
- timeout management
- retries
- idempotency
- routing

Never contains business rules.

---

## Application Services

Responsible for:

- orchestration
- transaction boundaries
- coordination between domain services
- integration sequencing

---

## Domain Services

Responsible for:

- business rules
- state transitions
- invariants
- persistence requests

Only Domain Services may change business state.

---

# 6. Tool Definition

Every tool SHALL expose:

- unique identifier
- name
- version
- input schema
- output schema
- permissions
- timeout
- idempotency policy
- execution metadata

---

# 7. Tool Categories

Examples:

Reservation Tools

Guest Tools

Pricing Tools

Messaging Tools

Cleaning Tools

Revenue Tools

Payment Tools

Analytics Tools

Each category maps to one application domain.

---

# 8. Tool Discovery

The Tool Dispatcher maintains the registry of available tools.

The AI never discovers tools dynamically.

The registry is deterministic and versioned.

---

# 9. Input Validation

Before invocation:

JSON Schema validation

↓

Required fields

↓

Type validation

↓

Permission validation

↓

Business validation

↓

Execution

Malformed requests are rejected before reaching business services.

---

# 10. Authorization

Execution requires:

Authenticated user

↓

Authorized organization

↓

Role validation

↓

Business policy validation

↓

Execution permission

Authorization occurs independently from AI reasoning.

---

# 11. Idempotency

Every mutating operation SHALL support idempotency.

Examples:

Reservation creation

Payment capture

Invoice generation

Duplicate requests must not produce duplicate side effects.

---

# 12. Transaction Management

Business transactions belong to Application Services.

The Tool Dispatcher never opens database transactions.

---

# 13. Error Categories

Execution failures are classified as:

Validation Error

Authorization Error

Business Rule Violation

Infrastructure Failure

External Integration Failure

Timeout

Unexpected Error

Each category follows an independent recovery strategy.

---

# 14. Retry Policy

Automatic retries are permitted only for:

Transient infrastructure failures

Network interruptions

Temporary provider failures

Business validation failures are never retried automatically.

---

# 15. External Tools

External providers are accessed exclusively through adapters.

Examples:

WhatsApp

Booking Platforms

Payment Gateways

Maps

Email

Weather

Provider SDKs never leak into business services.

---

# 16. Side Effects

Every side effect occurs only after successful business execution.

Examples:

Send WhatsApp

Publish Event

Trigger Workflow

Update Search Index

Notify Owner

Side effects never precede persistence.

---

# 17. Observability

Every execution records:

Tool ID

Execution ID

Correlation ID

Organization ID

Latency

Input Validation Result

Execution Result

Failure Reason

These records support tracing and auditing.

---

# 18. Security

The Tool Invocation Layer SHALL enforce:

Least privilege

Tenant isolation

Schema validation

Permission validation

Input sanitization

Audit logging

Execution security is mandatory.

---

# 19. Performance

Recommended targets:

Schema Validation

<5 ms

Tool Resolution

<10 ms

Authorization

<20 ms

Dispatcher Overhead

<20 ms

The execution layer should introduce minimal latency.

---

# 20. Versioning

Tool contracts are versioned.

Breaking changes require:

New version

↓

Compatibility period

↓

Migration

↓

Deprecation

Existing clients must continue functioning during supported compatibility windows.

---

# 21. Anti-Patterns

Forbidden:

AI executing SQL.

Business rules inside Tool Dispatcher.

Business validation inside prompts.

Tool-specific logic inside AI.

Direct provider SDK usage.

Database access from automation workflows.

Dynamic tool creation in production.

---

# 22. Future Evolution

Future capabilities may include:

Dynamic capability discovery

MCP-native tools

Cross-service invocation

Distributed execution

Remote tool catalogs

These enhancements shall preserve the execution contracts defined in this document.

---

# 23. Success Metrics

The Tool Invocation Layer is considered successful when:

- all business operations are validated before execution;
- execution remains deterministic;
- business logic is centralized in Domain Services;
- tools remain reusable and versioned;
- infrastructure concerns remain isolated from business concerns.

---

# 24. Architectural Summary

The Tool Invocation Layer separates reasoning from execution through clearly defined responsibilities.

AI proposes.

Validation authorizes.

The Tool Dispatcher routes.

Application Services coordinate.

Domain Services execute.

PostgreSQL persists.

Events communicate.

This execution chain guarantees operational safety while preserving flexibility and future extensibility.

---

# 25. Final Statement

The Tool Invocation & Execution Layer provides the deterministic execution backbone of ATLAS.

By isolating AI reasoning, business validation, application orchestration and domain execution into independent architectural layers, ATLAS achieves a secure, observable and maintainable execution model capable of supporting both MVP simplicity and future enterprise-scale evolution.
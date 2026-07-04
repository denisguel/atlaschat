# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0026
# DOCUMENT    : Workflow Responsibility Model
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
# This document formally defines the responsibilities of:
#
# • Backend
# • AI
# • n8n
# • External Services
#
# eliminating ambiguity regarding workflow ownership.
#
# Future versions of:
#
# - RFC-0010
# - ARCH-0012
# - ARCH-0016
# - ARCH-0018
# - ARCH-0020
#
# MUST reference this document.
#
# ============================================================================

# ARCH-0026

# WORKFLOW RESPONSIBILITY MODEL

---

# 1. Purpose

This document defines ownership boundaries between every execution component inside ATLAS.

Its objective is to guarantee that every business capability has exactly one responsible layer.

This avoids duplicated logic, hidden workflows and inconsistent behavior.

---

# 2. Motivation

One of the largest sources of technical debt in modern SaaS platforms is placing business logic inside automation tools.

Visual workflow engines make it easy to implement features quickly.

They also make it easy to distribute business rules across dozens of workflows.

As the platform grows this becomes impossible to maintain.

ATLAS explicitly forbids this architecture.

---

# 3. Core Principle

Every business rule belongs to the Backend.

Never to n8n.

---

# 4. Responsibility Matrix

| Component | Responsibility |
|------------|----------------|
| AI | Understand and propose |
| Decision Validator | Validate |
| Backend | Decide |
| Domain Services | Execute |
| PostgreSQL | Persist |
| Events | Notify |
| n8n | Orchestrate asynchronous workflows |
| External APIs | External capabilities |

Each responsibility exists exactly once.

---

# 5. AI Responsibilities

The AI may:

- understand intent
- summarize information
- classify requests
- draft responses
- recommend actions
- generate content
- propose execution plans

The AI SHALL NOT:

- execute business rules
- write directly to the database
- bypass validation
- modify financial state
- authorize operations

---

# 6. Backend Responsibilities

The Backend owns:

Business logic

Transactions

Authorization

Policies

Validation

Pricing

Reservation lifecycle

Availability

Payments

Invoices

Business state

The Backend is the operational brain of ATLAS.

---

# 7. Domain Services

Each Domain Service owns one bounded context.

Examples:

Reservation Service

Guest Service

Pricing Service

Payment Service

Cleaning Service

Messaging Service

No service may modify another service's internal state directly.

Communication occurs through contracts.

---

# 8. Decision Validator

The Decision Validator owns:

Risk evaluation

Approval policies

Human confirmation

Confidence thresholds

Semantic validation

Execution authorization

It does not execute operations.

---

# 9. Tool Dispatcher

The Tool Dispatcher owns:

Tool discovery

Parameter validation

Tool invocation

Timeouts

Retries

Idempotency

It is infrastructure.

Not business logic.

---

# 10. Workflow Engine

n8n owns orchestration.

Not decision making.

Examples:

Wait 24 hours

↓

Send reminder

↓

Wait payment

↓

Notify owner

↓

Trigger webhook

The workflow coordinates actions.

It never decides business rules.

---

# 11. What n8n MAY Do

Examples:

Send emails

Send WhatsApp notifications

Schedule reminders

Synchronize OTA calendars

Generate recurring tasks

Import CSV files

Call webhooks

Invoke APIs

Retry failed integrations

Execute long-running workflows

These are orchestration responsibilities.

---

# 12. What n8n SHALL NEVER Do

Examples:

Calculate pricing.

Approve refunds.

Validate reservations.

Authorize payments.

Determine cancellation policy.

Change business state.

Apply discounts.

Modify invoices.

Resolve booking conflicts.

Every one of these belongs to the Backend.

---

# 13. Why This Separation Exists

Business rules change frequently.

Workflow orchestration changes frequently.

Infrastructure changes frequently.

Each should evolve independently.

---

# 14. Workflow Lifecycle

```
Business Event

↓

Event Bus

↓

Workflow Trigger

↓

n8n

↓

External Actions

↓

Completion
```

The workflow begins after the transaction has already completed.

---

# 15. Retry Policy

Failures inside workflows never affect committed transactions.

Example:

Reservation created

↓

Commit

↓

Notification fails

↓

Retry notification

↓

Reservation remains valid

Operational state is never rolled back because of external failures.

---

# 16. Long Running Processes

Suitable examples:

Check-in reminders

Review requests

Payment reminders

Cleaning notifications

Marketing campaigns

Seasonal automations

These naturally belong to workflow orchestration.

---

# 17. Human Tasks

n8n may assign:

Cleaning

Maintenance

Inspections

Manual approvals

Owner follow-ups

Task assignment is orchestration.

Task definition belongs to business rules.

---

# 18. External Integrations

Every integration follows:

```
Backend

↓

Adapter

↓

n8n (optional)

↓

External API
```

The adapter isolates provider-specific behavior.

---

# 19. Failure Isolation

Workflow failures SHALL NOT:

Rollback reservations.

Rollback payments.

Rollback invoices.

Rollback database transactions.

Workflow reliability is independent from business consistency.

---

# 20. Monitoring

Every workflow stores:

Workflow ID

Correlation ID

Organization ID

Trigger Event

Execution Status

Retry Count

Execution Time

These metrics support observability.

---

# 21. Anti-Patterns

Forbidden:

Business rules inside n8n.

Database writes from workflows.

SQL executed from automation.

AI decisions inside workflow nodes.

Payment calculations inside visual flows.

Conditional pricing in automation tools.

Duplicated business validation.

---

# 22. Future Evolution

Future workflow engines may replace n8n.

Examples:

Temporal

Netflix Conductor

Azure Durable Functions

Google Workflows

The Backend architecture SHALL remain unchanged.

Only the orchestration layer changes.

---

# 23. Migration Strategy

The MVP uses n8n.

Future migration requires only replacing the orchestration engine.

Business logic remains untouched.

This minimizes migration cost.

---

# 24. Success Metrics

A healthy workflow architecture achieves:

- zero business logic inside workflows;
- independent deployment of automation;
- recoverable failures;
- observable executions;
- deterministic backend behavior.

---

# 25. Final Statement

ATLAS distinguishes between business execution and workflow orchestration.

Business decisions belong to deterministic services.

Artificial Intelligence proposes.

The Decision Validator authorizes.

The Backend executes.

n8n coordinates asynchronous activities after the business transaction has completed.

This separation prevents workflow sprawl, reduces technical debt and ensures that operational integrity is preserved regardless of the automation platform adopted in the future.
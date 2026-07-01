# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0022
# DOCUMENT    : Business Decision Validation Layer
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
# Future versions of:
#
# - RFC-0002
# - RFC-0010
# - ARCH-0012
# - ARCH-0016
# - ARCH-0020
#
# MUST reference this document.
#
# ============================================================================

# ARCH-0022

# BUSINESS DECISION VALIDATION LAYER

---

# 1. Purpose

This document defines the Business Decision Validation Layer (BDVL), a mandatory architectural component positioned between AI reasoning and deterministic business execution.

Its purpose is to ensure that no AI-generated decision can directly trigger business operations without first passing through explicit validation policies.

The BDVL transforms AI from an autonomous executor into a supervised decision-making system.

---

# 2. Motivation

Traditional AI architectures follow this execution model:

```
User

↓

LLM

↓

Tool

↓

Database
```

This architecture is dangerous.

An incorrect interpretation from the LLM can produce perfectly valid—but incorrect—business actions.

Examples include:

- cancelling reservations
- refunding payments
- modifying prices
- deleting guest information
- issuing invoices

The deterministic service executes exactly what it receives.

The error originates in the AI reasoning, not in the service.

Therefore, ATLAS introduces a mandatory validation layer.

---

# 3. Architectural Principle

The following rule is immutable:

```
AI NEVER EXECUTES.

AI PROPOSES.

THE SYSTEM VALIDATES.

THE BUSINESS RULES AUTHORIZE.

ONLY THEN MAY EXECUTION OCCUR.
```

No exception exists.

---

# 4. Position inside the Architecture

```
User

↓

AI Orchestrator

↓

Planner (optional)

↓

Specialized Agent

↓

Business Decision Validation Layer

↓

Tool Dispatcher

↓

Domain Service

↓

Database
```

The BDVL exists before every business action.

---

# 5. Responsibilities

The BDVL is responsible for validating:

- semantic correctness
- business policy compliance
- financial risk
- operational impact
- authorization requirements
- confidence thresholds
- regulatory constraints
- owner approval policies

The BDVL never performs reasoning.

It validates reasoning.

---

# 6. Non-Responsibilities

The BDVL SHALL NOT:

- generate responses
- execute business logic
- update databases
- call external APIs
- modify prompts
- replace the Planner
- replace Domain Services

Its only purpose is validation.

---

# 7. Validation Pipeline

Every decision follows the same lifecycle.

```
AI Proposal

↓

Semantic Validation

↓

Business Validation

↓

Risk Assessment

↓

Approval Policy

↓

Execution Authorization

↓

Domain Service
```

Failure at any stage stops execution.

---

# 8. Semantic Validation

The first validation verifies whether the proposed action accurately represents the user's intent.

Example:

Guest:

"I need to move my reservation."

LLM proposes:

Cancel Reservation

↓

Validation

↓

Rejected

↓

Clarification required

The AI does not execute.

---

# 9. Business Validation

Business rules are deterministic.

Examples:

Reservation already checked in?

↓

Modification prohibited

---

Refund exceeds payment?

↓

Reject

---

Reservation belongs to another tenant?

↓

Reject

---

Property unavailable?

↓

Reject

The AI cannot override business rules.

---

# 10. Risk Classification

Every action receives a risk level.

```
LOW

MEDIUM

HIGH

CRITICAL
```

Risk determines validation depth.

---

# 11. Low Risk

Examples:

Answer FAQ

Translate message

Summarize conversation

Draft response

Generate Instagram caption

Execution:

Automatic

No confirmation required.

---

# 12. Medium Risk

Examples:

Send WhatsApp message

Update guest note

Create cleaning task

Modify check-in instructions

Execution:

Policy validation required.

---

# 13. High Risk

Examples:

Reservation modification

Price adjustment

Availability changes

Coupon generation

Execution:

Policy validation

+

Confirmation

---

# 14. Critical Risk

Examples:

Cancel reservation

Delete reservation

Issue refund

Delete guest

Delete property

Payment reversal

Execution:

Human approval required.

Always.

---

# 15. Confidence Threshold

Every AI decision includes a confidence score.

Example:

```
0.97

High confidence

↓

Continue
```

---

```
0.63

Low confidence

↓

Clarify with user
```

Confidence alone never authorizes execution.

It is only one signal.

---

# 16. Business Confidence

ATLAS distinguishes between:

AI confidence

and

Business confidence.

Example:

The AI may be 99% certain the user wants to cancel.

However,

the business impact is critical.

Human confirmation is still mandatory.

Business risk always overrides AI confidence.

---

# 17. Human Approval Policies

Approval policies are configurable.

Examples:

Owner approval

Manager approval

Two-step approval

Automatic approval

Scheduled approval

Each organization may define its own governance.

---

# 18. Clarification Policy

When ambiguity exists,

the system MUST ask.

Never assume.

Example:

```
Guest:

I need to change my reservation.
```

↓

System:

"Would you like to change your dates or cancel your reservation?"

This is cheaper than executing incorrectly.

---

# 19. Financial Guardrails

Financial operations require additional validation.

Examples:

Refund

↓

Payment exists?

↓

Refund limit?

↓

Fraud policy?

↓

Approval?

↓

Execute

No AI model may bypass financial controls.

---

# 20. Operational Guardrails

Operational actions also require protection.

Examples:

Generate cleaning task

Check property occupancy

Lock smart devices

Modify pricing

Each action has predefined policies.

---

# 21. Explainability

Every validated decision produces an audit record.

Example:

AI Proposal

↓

Validation Result

↓

Business Rule Applied

↓

Approval Policy

↓

Final Decision

Every action is explainable.

---

# 22. Audit Trail

Every validation stores:

- timestamp
- organization_id
- actor
- proposed action
- validation results
- applied policies
- approval chain
- execution result

Audit logs are immutable.

---

# 23. Failure Modes

Validation may produce:

APPROVED

REJECTED

REQUIRES_CONFIRMATION

REQUIRES_APPROVAL

REQUIRES_CLARIFICATION

NO_EXECUTION

These outcomes are deterministic.

---

# 24. Anti-Patterns

The following are prohibited:

Executing directly from AI.

Trusting confidence scores blindly.

Using prompts instead of policies.

Embedding business rules inside prompts.

Allowing AI to override deterministic services.

Skipping confirmation for destructive actions.

---

# 25. Implementation Rules

The MVP SHALL implement the BDVL as a dedicated application layer.

It may initially be implemented as a policy engine inside the backend.

Future versions may evolve into an independent service.

The interface must remain stable.

---

# 26. Success Metrics

The BDVL is considered successful when:

- zero irreversible actions occur without validation;
- every destructive action is auditable;
- ambiguous requests always generate clarification;
- business rules consistently override AI reasoning when necessary;
- owners trust AI-assisted automation because every critical action is governed by explicit policies.

---

# 27. Final Statement

The Business Decision Validation Layer is one of the fundamental architectural components of ATLAS.

It establishes a strict separation between intelligence and authority.

Artificial Intelligence is responsible for understanding.

Business policies are responsible for deciding.

Deterministic services are responsible for executing.

This separation enables ATLAS to scale AI capabilities while maintaining operational safety, regulatory compliance, explainability and user trust.
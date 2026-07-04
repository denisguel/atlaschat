# RFC-0002_AI_ORCHESTRATION.md

---
id: RFC-0002
title: AI Orchestration Engine
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: AI Architecture
type: Request For Comments
classification: Critical
related:
  - RFC-0001
  - ADR-004
  - ADR-005
  - ADR-007
  - ADR-008
  - ARCH-012
---

# 1. Purpose

This RFC defines the AI Orchestration Engine of ATLAS.

The Orchestrator is the cognitive control layer responsible for transforming user intent into deterministic business execution.

It coordinates:

- conversation understanding
- memory retrieval
- planning
- agent delegation
- tool selection
- execution supervision
- response generation

The Orchestrator is the "brain of the brains."

---

# 2. Scope

This RFC defines:

- Orchestrator responsibilities
- execution lifecycle
- planning model
- context assembly
- routing strategy
- execution contracts
- interaction with Agents
- interaction with Services
- interaction with Brains

This RFC does NOT define:

- individual agent behavior
- business rules
- UI
- workflows

---

# 3. Architectural Position

```
Conversation Layer
        │
        ▼
 AI Orchestrator
        │
 ┌──────┼─────────────┐
 ▼      ▼             ▼
Context Agents     Tools
        │
        ▼
Domain Services
        │
        ▼
Event Bus
```

The Orchestrator owns coordination.

It does not own business logic.

---

# 4. Core Responsibilities

The Orchestrator is responsible for:

- understanding requests
- retrieving context
- selecting agents
- creating execution plans
- selecting tools
- validating execution
- composing responses
- escalating when required

---

# 5. Guiding Principles

The Orchestrator must be:

- deterministic whenever possible
- explainable
- observable
- provider agnostic
- stateless
- secure
- tenant aware

---

# 6. High-Level Pipeline

Every interaction follows the same lifecycle.

```
User Input

↓

Intent Detection

↓

Context Retrieval

↓

Constraint Resolution

↓

Execution Planning

↓

Agent Selection

↓

Tool Selection

↓

Execution

↓

Validation

↓

Response Generation
```

---

# 7. Intent Resolution

Every interaction begins by identifying intent.

Example:

```
"Can I increase next weekend's price?"

↓

Intent:

PRICE_OPTIMIZATION
```

Intent classification should include:

- primary intent
- secondary intent
- urgency
- confidence
- required context

---

# 8. Context Assembly

The Context Builder retrieves only the minimum required information.

Possible sources:

Guest Brain

Property Brain

Growth Brain

Reservation History

Recent Conversation

Pricing State

Business Rules

System Constraints

Current Time

Context must be:

- relevant
- minimal
- versioned
- explainable

---

# 9. Constraint Resolution

Before planning, constraints are evaluated.

Examples:

- user permissions
- property restrictions
- reservation conflicts
- legal limitations
- pricing rules
- tenant configuration

No execution plan may violate platform constraints.

---

# 10. Planning Engine

The Planning Engine converts intent into executable steps.

Example:

```
Intent

↓

Retrieve Calendar

↓

Retrieve Occupancy

↓

Revenue Agent

↓

Pricing Tool

↓

Reservation Service

↓

Generate Explanation
```

Planning must occur before execution.

---

# 11. Planning Principles

Plans must be:

- deterministic
- observable
- interruptible
- resumable
- explainable

Every plan receives:

- plan_id
- timestamp
- version
- execution state

---

# 12. Agent Selection

The Orchestrator selects one or more domain agents.

Selection criteria:

- domain ownership
- required capabilities
- confidence
- cost
- latency
- context availability

Agents never self-select.

---

# 13. Multi-Agent Coordination

Simple requests:

```
Owner

↓

Guest Agent
```

Complex requests:

```
Owner

↓

Orchestrator

↓

Revenue Agent

↓

Operations Agent

↓

Content Agent

↓

Unified Response
```

The Orchestrator owns coordination.

---

# 14. Tool Selection

Agents never invoke arbitrary tools.

The Orchestrator validates:

- permissions
- tenant
- input schema
- tool version
- execution safety

Only then is execution allowed.

---

# 15. Execution Lifecycle

```
Plan Created

↓

Agent Executes

↓

Tool Executes

↓

Service Executes

↓

Domain Event

↓

Validation

↓

Memory Update

↓

Response
```

Each step is observable.

---

# 16. Human Escalation

The Orchestrator may stop execution.

Examples:

- low confidence
- conflicting information
- destructive action
- missing permissions
- ambiguous request

Instead of guessing, it asks for clarification.

---

# 17. Confidence Model

Each decision includes a confidence score.

Suggested ranges:

95–100%

Automatic execution.

---

75–95%

Execute with explanation.

---

50–75%

Ask for confirmation.

---

Below 50%

Escalate.

Confidence is advisory.

It does not replace authorization.

---

# 18. Error Recovery

Possible failures:

Agent failure

↓

Retry

---

Tool failure

↓

Fallback tool

---

Provider failure

↓

Alternative provider

---

Execution failure

↓

Rollback if possible

↓

Explain to user

The platform should degrade gracefully.

---

# 19. AI Provider Independence

The Orchestrator never references provider-specific APIs.

Instead:

```
Agent

↓

Intelligence Layer

↓

Provider Adapter

↓

LLM
```

This preserves vendor independence.

---

# 20. Memory Interaction

The Orchestrator never stores long-term memory.

Memory lifecycle:

```
Retrieve

↓

Use

↓

Generate Event

↓

Memory Pipeline Updates Brain
```

Brains remain independent systems.

---

# 21. Security

Before every execution:

Validate:

- authentication
- authorization
- tenant ownership
- tool permissions
- policy compliance

AI cannot bypass platform security.

---

# 22. Observability

Every execution records:

- execution_id
- conversation_id
- tenant_id
- user_id
- model
- provider
- agent list
- tools used
- latency
- token usage
- cost estimate
- execution result

Every AI decision is traceable.

---

# 23. Performance Targets

Intent detection:

< 200 ms

Context retrieval:

< 500 ms

Planning:

< 300 ms

Typical AI response:

< 2 seconds

Complete conversational response:

< 3 seconds

---

# 24. Anti-Patterns

Forbidden:

- agents selecting themselves
- direct database access
- hidden reasoning
- provider-specific prompts
- unrestricted context loading
- executing without validation
- mixing planning with business logic

---

# 25. Future Evolution

The Orchestrator is designed to evolve toward:

- autonomous planning
- adaptive reasoning
- dynamic agent creation
- recursive planning
- self-optimization
- predictive workflows
- MCP-native execution
- distributed orchestration

No architectural redesign should be required.

---

# 26. Success Criteria

The AI Orchestrator is considered correctly implemented when:

- every request follows the orchestration pipeline;
- context retrieval is selective;
- planning occurs before execution;
- domain agents remain specialized;
- services remain deterministic;
- every decision is observable and explainable;
- provider replacement requires no orchestration redesign.

---

# 27. Summary

The AI Orchestrator is the cognitive control center of ATLAS.

It transforms human intent into safe, explainable and deterministic execution by coordinating specialized agents, contextual memory, domain services and infrastructure while remaining provider-agnostic and future-compatible with MCP and autonomous AI systems.
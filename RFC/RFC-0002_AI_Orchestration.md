# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : RFC-0002
# DOCUMENT    : AI Orchestrator
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Request Orchestration
#
# REPLACES
#
# RFC-0002_AI_ORCHESTRATOR.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# ARCH-0021  AI Execution Levels
# ARCH-0022  Business Decision Validation Layer
# ARCH-0024  State Consistency Model
# ARCH-0025  MVP Physical Architecture
# ARCH-0026  Workflow Responsibility Model
# ARCH-0027  AI Cost Optimization Strategy
# ARCH-0028  AI Model Routing & Provider Strategy
# ARCH-0029  Context Retrieval Strategy
#
# ============================================================================

# RFC-0002

# AI ORCHESTRATOR

---

# 1. Purpose

This document defines the responsibilities, lifecycle and architectural contracts of the AI Orchestrator.

The AI Orchestrator is the central coordination component responsible for transforming an incoming request into a deterministic execution pipeline.

It coordinates execution.

It does not execute business logic.

---

# 2. Motivation

ATLAS is an AI-native operating system.

Every interaction must be:

- predictable
- observable
- cost-efficient
- secure
- deterministic

The AI Orchestrator guarantees these properties by coordinating specialized architectural components.

---

# 3. Responsibilities

The AI Orchestrator SHALL:

- receive every AI request;
- classify execution complexity;
- determine the execution level;
- coordinate context retrieval;
- invoke planning when required;
- coordinate specialized agents;
- submit proposed actions for validation;
- return the final response.

---

# 4. Non-Responsibilities

The AI Orchestrator SHALL NOT:

- execute business rules;
- persist operational data;
- bypass validation policies;
- invoke external APIs directly;
- calculate pricing;
- authorize financial operations;
- own business state.

---

# 5. Architectural Position

```
Incoming Request

↓

AI Orchestrator

↓

Execution Strategy

↓

Context Builder

↓

Planner (optional)

↓

Specialized Agents (optional)

↓

Business Decision Validation Layer

↓

Tool Dispatcher

↓

Domain Services

↓

PostgreSQL

↓

Domain Events

↓

Workflow Engine
```

---

# 6. Execution Lifecycle

Every request follows the same high-level lifecycle:

1. Receive request.
2. Normalize input.
3. Resolve execution level.
4. Build context.
5. Execute reasoning.
6. Validate proposed actions.
7. Execute deterministic services.
8. Generate response.
9. Emit events when applicable.

---

# 7. Request Classification

Incoming requests are classified according to:

- complexity;
- operational impact;
- business domains involved;
- required reasoning depth;
- expected latency;
- execution cost.

Classification determines the execution path.

---

# 8. Execution Levels

Execution Levels are defined by:

ARCH-0021

The Orchestrator SHALL NOT implement custom execution logic outside the levels defined by that specification.

---

# 9. Context Coordination

The Orchestrator requests context through the Context Builder.

Context retrieval SHALL follow:

ARCH-0029

The Orchestrator never performs retrieval directly.

---

# 10. Planner Invocation

Planner execution is optional.

The Planner SHALL execute only when required by the Execution Level.

Planner behavior is defined by:

ARCH-0021

---

# 11. Specialized Agents

The Orchestrator coordinates specialized agents.

Examples include:

- Guest Agent
- Reservation Agent
- Revenue Agent
- Operations Agent
- Content Agent

Agents collaborate logically.

Physical deployment is defined by:

ARCH-0025

---

# 12. Decision Validation

Any operation capable of modifying business state SHALL pass through the Business Decision Validation Layer.

Validation behavior is defined by:

ARCH-0022

The Orchestrator SHALL NEVER bypass validation.

---

# 13. Tool Invocation

After successful validation:

The Orchestrator delegates execution to the Tool Dispatcher.

The Tool Dispatcher is responsible for infrastructure concerns only.

Business execution remains inside Domain Services.

---

# 14. Domain Services

The Orchestrator communicates with Domain Services exclusively through defined application interfaces.

It never performs direct database operations.

---

# 15. Workflow Coordination

Long-running workflows SHALL NOT remain inside the synchronous execution pipeline.

After transaction completion:

Domain Events may trigger asynchronous workflows.

Workflow ownership is defined by:

ARCH-0026

---

# 16. State Consistency

Operational state SHALL be persisted according to:

ARCH-0024

The Orchestrator SHALL assume PostgreSQL as the single source of truth.

Events are published only after successful transaction commit.

---

# 17. Cost Optimization

The Orchestrator SHALL minimize:

- AI calls;
- retrieved context;
- planner invocations;
- multi-agent execution;
- token consumption.

Optimization policies are defined by:

ARCH-0027

---

# 18. Provider Selection

The Orchestrator SHALL NOT select AI providers directly.

Provider selection is delegated to the Model Router.

Routing behavior is defined by:

ARCH-0028

---

# 19. Error Handling

The Orchestrator distinguishes:

- validation failures;
- reasoning failures;
- infrastructure failures;
- provider failures;
- workflow failures.

Each category follows an independent recovery strategy.

Business consistency always takes precedence over conversational continuity.

---

# 20. Observability

Every orchestration cycle SHALL generate:

- Request ID
- Correlation ID
- Organization ID
- Execution Level
- Retrieved Context Size
- Planner Usage
- Selected Model
- Token Consumption
- Total Latency
- Validation Result
- Execution Result

These metrics support monitoring, optimization and auditing.

---

# 21. Security

The Orchestrator SHALL enforce:

- authentication;
- authorization;
- tenant isolation;
- execution policies;
- organizational boundaries.

No request may bypass security controls.

---

# 22. Performance Targets

Recommended synchronous targets:

Intent Classification

<20 ms

Context Retrieval

<300 ms

Planner

<300 ms

LLM

<1500 ms

Validation

<100 ms

Total target

<3 seconds

Latency optimization SHALL never compromise business correctness.

---

# 23. Anti-Patterns

The following are prohibited:

Embedding business rules inside prompts.

Direct database writes.

Provider-specific orchestration.

Planner-first execution.

Mandatory multi-agent execution.

Unlimited context retrieval.

Skipping validation.

Duplicating business logic.

Using AI to replace deterministic software.

---

# 24. Success Metrics

The AI Orchestrator is considered successful when:

- requests are routed to the appropriate execution level;
- latency objectives are consistently achieved;
- AI costs remain predictable;
- validation policies are always enforced;
- business consistency is preserved;
- providers remain interchangeable;
- orchestration remains observable and auditable.

---

# 25. Final Statement

The AI Orchestrator is the central coordination layer of ATLAS.

It transforms user requests into deterministic execution pipelines by coordinating execution levels, context retrieval, planning, specialized agents, validation and business services while remaining independent from business rules, persistence and infrastructure providers.

This separation of responsibilities enables ATLAS to deliver scalable, observable and cost-efficient AI capabilities without compromising operational integrity, architectural simplicity or future extensibility.
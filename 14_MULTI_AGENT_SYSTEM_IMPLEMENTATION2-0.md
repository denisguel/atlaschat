# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : DOC-0014
# DOCUMENT    : Multi-Agent System Implementation
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : AI Architecture
#
# REPLACES
#
# 14_MULTI_AGENT_SYSTEM_IMPLEMENTATION.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0002 AI Orchestrator
# RFC-0003 Memory System
# RFC-0010 Tool Invocation & Execution Layer
#
# ARCH-0021 AI Execution Levels
# ARCH-0022 Business Decision Validation Layer
# ARCH-0025 MVP Physical Architecture
# ARCH-0028 AI Model Routing
# ARCH-0029 Context Retrieval Strategy
#
# ============================================================================

# MULTI-AGENT SYSTEM IMPLEMENTATION

---

# 1. Purpose

This document defines how ATLAS implements its AI multi-agent architecture.

The objective is to obtain specialization without introducing unnecessary complexity, latency or infrastructure costs.

The MVP uses a **logical multi-agent architecture deployed as a single application**.

---

# 2. Core Principle

ATLAS implements:

Logical Multi-Agent

NOT

Physical Multi-Agent.

Agents are software components.

They are **not** independent services.

They are **not** independent processes.

They are **not** autonomous applications.

---

# 3. Why

Traditional AI systems use one gigantic prompt.

ATLAS separates reasoning into specialized domains.

Benefits include:

- better prompts
- better context
- smaller context windows
- lower token usage
- improved maintainability
- domain specialization

---

# 4. Agent Philosophy

Each agent knows one business domain extremely well.

No agent knows the complete platform.

Knowledge specialization reduces hallucinations.

---

# 5. Agent Characteristics

Every agent is:

Stateless

Deterministic Inputs

Deterministic Outputs

Tool Restricted

Domain Specialized

Observable

Replaceable

Versioned

Agents never own business state.

---

# 6. Current Agents

Initial agents include:

Guest Agent

Reservation Agent

Revenue Agent

Growth Agent

Operations Agent

Property Agent

Knowledge Agent

Future versions may introduce additional specialized agents.

---

# 7. Guest Agent

Responsible for:

Guest communication

Tone

Hospitality

FAQ

Recommendations

Conversation quality

It never creates reservations directly.

---

# 8. Reservation Agent

Responsible for:

Availability reasoning

Reservation modifications

Booking policies

Reservation validation

Reservation explanations

Execution always occurs through Application Services.

---

# 9. Revenue Agent

Responsible for:

Pricing reasoning

Revenue recommendations

Demand analysis

Pricing explanations

Revenue simulations

It never changes prices directly.

---

# 10. Growth Agent

Responsible for:

Marketing

Lead conversion

Campaign reasoning

Attribution

Content recommendations

Commercial optimization

Business execution remains deterministic.

---
# 11. Operations Agent

The Operations Agent specializes in day-to-day property operations.

Responsibilities include:

- housekeeping coordination;
- maintenance planning;
- check-in readiness;
- check-out verification;
- operational task prioritization;
- operational recommendations.

The Operations Agent never executes operational actions directly.

---

# 12. Property Agent

The Property Agent specializes in property knowledge.

Responsibilities include:

- amenities;
- policies;
- equipment;
- local recommendations;
- house rules;
- operational documentation.

It retrieves knowledge through the Context Builder.

---

# 13. Knowledge Agent

The Knowledge Agent specializes in semantic retrieval.

Responsibilities include:

- retrieving relevant memories;
- retrieving documentation;
- semantic search;
- knowledge summarization;
- source attribution.

It never performs business reasoning.

---

# 14. Agent Invocation

Agents are not permanently active.

They are invoked only when required.

Decision flow:

```
User Request

↓

AI Orchestrator

↓

Intent Classification

↓

Execution Level Selection

↓

Context Retrieval

↓

Agent Selection

↓

Agent Execution

↓

Business Validation

↓

Tool Invocation

↓

Response
```

This minimizes latency and token consumption.

---

# 15. Single-Agent Execution

Most user interactions require only one specialized agent.

Examples:

Guest asks Wi-Fi password

↓

Guest Agent

--------------------------------

Owner asks today's occupancy

↓

Reservation Agent

--------------------------------

Owner asks tomorrow's pricing

↓

Revenue Agent

Single-agent execution is the default behavior.

---

# 16. Multi-Agent Collaboration

Multiple agents are invoked only when justified.

Example:

Owner asks:

"Should I lower prices this weekend and launch an Instagram campaign?"

Execution:

Revenue Agent

↓

Growth Agent

↓

Recommendation Merge

↓

Business Validation

↓

Response

Parallel reasoning is preferred whenever dependencies do not exist.

---

# 17. AI Orchestrator

The AI Orchestrator coordinates the entire execution lifecycle.

Responsibilities include:

- intent classification;
- execution level selection;
- model routing;
- context budget allocation;
- agent routing;
- response aggregation.

The Orchestrator never performs domain reasoning.

---

# 18. Context Builder

Agents never query databases directly.

All contextual information is assembled by the Context Builder.

Possible sources include:

PostgreSQL

Revenue Brain

Growth Brain

Guest Brain

Property Brain

Configuration

Conversation History

External Signals

Only relevant context is delivered to each agent.

---

# 19. Planner

Planning is optional.

The Planner is invoked only when execution complexity exceeds predefined thresholds.

Typical scenarios:

Multi-step workflows

Cross-domain reasoning

Long-running operations

Otherwise the Planner is skipped.

---

# 20. Agent Communication

Agents do not communicate directly.

All communication flows through the AI Orchestrator.

This prevents:

Circular dependencies

Hidden coupling

Uncontrolled context growth

Non-deterministic behavior

The Orchestrator remains the single coordination layer.

---
# 21. Business Decision Validation

Before any business action is executed, recommendations pass through the Business Decision Validation Layer.

Validation includes:

- Business Policy Compliance
- Permission Verification
- Operational Constraints
- Financial Limits
- Reservation Integrity
- Human Approval Policies

This prevents AI reasoning errors from producing irreversible business actions.

---

# 22. Tool Invocation

Agents never call external systems directly.

All execution flows through the Tool Invocation Layer.

Execution sequence:

Agent Decision

↓

Business Validation

↓

Tool Dispatcher

↓

Application Service

↓

Database Transaction

↓

Domain Event

↓

Workflow Trigger

↓

Response

This guarantees deterministic execution.

---

# 23. Stateless Execution

Each execution is independent.

Agents do not retain runtime memory between requests.

Persistent knowledge is retrieved from:

- Revenue Brain
- Growth Brain
- Guest Brain
- Property Brain

Operational state is always retrieved from PostgreSQL.

---

# 24. Execution Levels

Every request receives an Execution Level before agent selection.

Examples:

Level 1

Simple FAQ

↓

One lightweight model

↓

Single Agent

--------------------------------

Level 2

Operational Question

↓

One Specialized Agent

↓

Database Access

--------------------------------

Level 3

Cross-Domain Analysis

↓

Multiple Agents

↓

Planner (optional)

↓

Business Validation

Execution Levels minimize latency and AI costs.

---

# 25. Model Routing

Different agents may use different AI models.

Examples:

Guest Agent

↓

Fast Low-Cost Model

--------------------------------

Revenue Agent

↓

High-Reasoning Model

--------------------------------

Knowledge Agent

↓

Embedding + Lightweight Model

Model selection follows ARCH-0028.

---

# 26. Failure Handling

Agent failures are isolated.

Possible strategies include:

Retry

Fallback Model

Simplified Reasoning

Owner Notification

Graceful Degradation

A single agent failure shall not compromise the entire request whenever possible.

---

# 27. Observability

Every execution records:

Execution ID

Execution Level

Invoked Agents

Model Used

Prompt Tokens

Completion Tokens

Latency

Retrieved Context

Validation Outcome

Tool Calls

Business Result

These metrics support optimization and troubleshooting.

---

# 28. Security

Agents SHALL enforce:

Tenant Isolation

Role Permissions

Tool Permissions

Prompt Isolation

Context Isolation

Agents never receive data belonging to another organization.

---

# 29. Anti-Patterns

Forbidden:

Agents writing directly to PostgreSQL.

Agents invoking APIs directly.

Agents bypassing Business Validation.

Persistent agent memory.

Cross-agent hidden communication.

Agent-specific business state.

Unlimited context windows.

Always invoking multiple agents.

Treating every request as a planning problem.

---

# 30. Future Evolution

Future versions may introduce:

Dynamic Agent Creation

Hierarchical Agent Teams

Specialized Industry Agents

Voice Agents

Vision Agents

MCP-based External Agents

Distributed Agent Execution

Self-Evaluation Agents

All future evolution shall preserve deterministic execution, centralized orchestration and business validation.

---

# 31. Final Statement

The Multi-Agent System enables ATLAS to combine domain-specialized AI reasoning with deterministic business execution.

By keeping agents stateless, centrally orchestrated and constrained by explicit validation layers, the platform achieves enterprise-grade intelligence while maintaining low latency, predictable costs and operational reliability suitable for the MVP and future evolution.
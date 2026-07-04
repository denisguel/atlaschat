# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
# DOCUMENT ID : ARCH-0021
# DOCUMENT    : AI Execution Levels
# VERSION     : 2.0
# STATUS      : ACCEPTED
# AUTHOR      : Architecture Board
# CLASS       : Core Architecture
# ============================================================================

# ARCH-0021
# AI EXECUTION LEVELS

---

# 1. Purpose

This document defines the execution model used by every AI request inside ATLAS.

It replaces the implicit assumption that every interaction requires the complete AI architecture.

Instead, ATLAS executes requests through progressive intelligence levels, selecting the minimum amount of reasoning required to solve the user's problem while minimizing latency, infrastructure cost and cognitive complexity.

This specification becomes mandatory for every AI interaction inside the platform.

---

# 2. Motivation

One of the most common mistakes in AI systems is assuming that every request deserves maximum intelligence.

That assumption creates:

• excessive latency

• excessive token consumption

• unnecessary LLM calls

• poor UX

• expensive infrastructure

• difficult debugging

• unpredictable execution

A hospitality operating system receives thousands of requests that do not require deep reasoning.

Examples:

• "Gracias"

• "¿Cuál es el wifi?"

• "¿A qué hora es el check-in?"

Running a Planner, multiple Agents and several Brain queries for these requests is architectural waste.

The execution engine must therefore adapt its intelligence to the complexity of the task.

---

# 3. Design Principles

The execution architecture follows seven immutable principles.

## Principle 1

Start with the simplest possible execution.

Never assume complexity.

---

## Principle 2

Escalate only when evidence requires it.

---

## Principle 3

Reasoning is expensive.

Business value must justify reasoning cost.

---

## Principle 4

Memory retrieval is selective.

Never load all Brains.

---

## Principle 5

Multiple Agents are exceptional.

Single-agent execution is the default.

---

## Principle 6

Planning is expensive.

Planning is never executed automatically.

---

## Principle 7

AI quality is measured by business outcome.

Not by prompt complexity.

---

# 4. The AI Execution Pyramid

The execution architecture is represented as a pyramid.

```

```
                    L4
          Strategic Intelligence
          Portfolio Optimization

                 ▲

                 L3
      Multi-Agent Deliberative Reasoning

                 ▲

                 L2
      Contextual Single-Agent Execution

                 ▲

                 L1
         Single LLM Inference

                 ▲

                 L0
     Deterministic Execution Engine
```

Higher levels provide more intelligence.

Lower levels provide more speed.

The system always starts from the bottom.

Never from the top.

---

# 5. Architectural Goal

The objective is not maximizing intelligence.

The objective is maximizing:

```
Business Value

──────────────────────

Latency

+

Cost

+

Complexity
```

A simpler solution is always preferred if the business outcome is identical.

---

# 6. Execution Lifecycle

Every request follows the same lifecycle.

```
Incoming Request

↓

Normalization

↓

Intent Detection

↓

Complexity Estimation

↓

Execution Level Selection

↓

Execution

↓

Decision Validation

↓

Response

↓

Learning
```

Only the execution phase changes.

The surrounding architecture remains identical.

---

# 7. Complexity Estimation

Before selecting an execution level, the Orchestrator computes a Complexity Score.

Inputs include:

• ambiguity

• number of business domains involved

• required memory

• expected reasoning depth

• financial impact

• operational impact

• confidence score

• historical similarity

This score determines the minimum execution level required.

No Planner is involved in this decision.

---

# 8. Execution Objectives

Each execution level optimizes different goals.

| Level | Goal |
|--------|------|
| L0 | Speed |
| L1 | Natural language |
| L2 | Contextual understanding |
| L3 | Multi-domain reasoning |
| L4 | Strategic optimization |

Escalation must always be justified.

Never automatic.

---

# 9. Global Performance Targets

Median latency:

L0

<100 ms

L1

<1 second

L2

<2 seconds

L3

2–5 seconds

L4

Background execution

No user should wait for strategic reasoning.

---

# 10. Cost Budget Philosophy

Execution cost grows exponentially.

```
L0

≈ zero

↓

L1

small

↓

L2

medium

↓

L3

high

↓

L4

very high
```

Therefore:

Most requests should terminate before reaching L2.

L3 should remain exceptional.

L4 should almost never execute synchronously.

---

# 11. Success Metrics

A production deployment is considered healthy when:

• >70% of requests finish at L0 or L1

• <20% require L2

• <10% require L3

• <1% require L4

If L3 becomes common, architecture must be reviewed.

Because the system is reasoning too much instead of designing better tools.

---
# 12. Level L0 — Deterministic Execution

---

## Purpose

L0 exists to eliminate unnecessary AI usage.

The overwhelming majority of requests received by a hospitality platform do **not require reasoning**.

They require deterministic execution.

L0 guarantees:

- near-zero latency
- zero LLM cost
- predictable behavior
- maximum throughput

L0 is the preferred execution path.

---

## Philosophy

If software can answer correctly without AI,

AI must not execute.

Artificial Intelligence is never used to compensate for poor software engineering.

---

## Components

L0 consists of:

```
Request

↓

Normalizer

↓

Intent Matcher

↓

Rule Engine

↓

Template Engine

↓

Response
```

No LLM exists in this pipeline.

---

## Typical Requests

Examples:

Guest:

```
Gracias
```

↓

```
¡De nada!
```

---

Guest

```
¿Cuál es el horario del check-in?
```

↓

Static Knowledge Base

↓

Response

---

Guest

```
¿Aceptan mascotas?
```

↓

Property Configuration

↓

Response

---

Guest

```
¿Cuál es la contraseña del WiFi?
```

↓

Property Metadata

↓

Response

---

Guest

```
Enviar ubicación
```

↓

Google Maps URL

↓

Response

---

## Allowed Resources

L0 may access:

✓ configuration

✓ cached metadata

✓ FAQ

✓ templates

✓ property settings

✓ reservation status cache

L0 cannot query:

✗ Guest Brain

✗ Growth Brain

✗ Planner

✗ Vector Search

✗ AI Models

---

## Target Metrics

Latency

<100 ms

Availability

99.99%

Cost

≈ zero

---

# 13. Level L1 — Direct AI Inference

---

## Purpose

L1 introduces a single LLM.

No planning.

No multi-agent collaboration.

No orchestration.

The objective is natural language understanding.

---

## Pipeline

```
Request

↓

Prompt Builder

↓

LLM

↓

Response
```

Nothing else.

---

## Characteristics

One model.

One prompt.

One response.

---

## Typical Use Cases

Guest asks:

```
¿Cómo llego desde Buenos Aires?
```

↓

LLM answers.

---

Guest asks:

```
¿Qué restaurantes recomendás?
```

↓

LLM answers.

---

Owner asks

```
Escribime un mensaje amable.
```

↓

LLM writes message.

---

Owner asks

```
Traducí esto al inglés.
```

↓

LLM responds.

---

## Forbidden Operations

L1 cannot:

Modify reservations.

Create payments.

Cancel bookings.

Change pricing.

Access operational services.

Trigger workflows.

Every action is informational.

---

## Context

Prompt only.

No memory retrieval.

No Brain queries.

---

## Cost Target

One inference only.

Never chain prompts.

---

## Performance

<1 second median.

---

# 14. Level L2 — Contextual Execution

---

## Purpose

L2 introduces contextual intelligence.

This is the first level capable of consulting institutional knowledge.

Still,

only one Agent executes.

---

## Pipeline

```
Request

↓

Intent Detection

↓

Context Builder

↓

Selective Retrieval

↓

Single Agent

↓

Response
```

---

Notice what is missing.

There is:

NO Planner

NO Multi-Agent

NO Debate

NO Execution Plan

---

## Selective Context

The Context Builder retrieves only what is necessary.

Example

Guest asks:

```
¿A qué hora quedamos la última vez?
```

Only Guest Brain is queried.

---

Guest asks

```
¿Hay cochera?
```

Only Property Brain.

---

Guest asks

```
¿Cuánto pagué la última vez?
```

Reservation History only.

---

The Context Builder never loads every Brain.

---

## Retrieval Budget

Maximum:

One Brain

↓

One Entity

↓

One Time Window

↓

One Retrieval

This dramatically reduces latency.

---

## Agent

Exactly one.

Example:

Guest Agent.

Revenue Agent.

Operations Agent.

Content Agent.

Only one executes.

---

## Latency Budget

Context Retrieval

<300 ms

LLM

≈1 second

Total

<2 seconds.

---

## Typical Requests

```
¿Cuándo fue mi última reserva?
```

↓

Guest Brain

↓

Guest Agent

↓

Response

---

```
¿Cuántas personas entran en esta unidad?
```

↓

Property Brain

↓

Response

---

```
¿Cuánto facturé este mes?
```

↓

Revenue Context

↓

Revenue Agent

↓

Response

---

## Why L2 Exists

Because 90% of business questions require memory,

but not planning.

Without L2,

every contextual request would incorrectly escalate to L3.

That would multiply infrastructure cost while degrading user experience.

---
# 15. Level L3 — Deliberative Multi-Agent Execution

---

## Purpose

L3 is reserved for requests that require reasoning across multiple business domains.

Unlike L2, where a single specialized agent solves the problem using limited context, L3 coordinates multiple logical agents through a planning phase.

This level exists to solve complex operational decisions—not routine conversations.

Examples include:

- Reservation modifications with pricing implications
- Multi-property recommendations
- Revenue optimization scenarios
- Operational conflicts
- Cross-domain planning

L3 is expensive and therefore exceptional.

---

## Execution Pipeline

```
Incoming Request

↓

Complexity Evaluation

↓

Planner

↓

Execution Plan

↓

Agent Router

↓

Agent A

↓

Agent B

↓

Agent C

↓

Plan Aggregation

↓

Decision Validator

↓

Response
```

The Planner is only invoked after the request has been classified as requiring L3.

---

## Planner Responsibilities

The Planner never executes business logic.

Its only responsibilities are:

- identify objectives
- identify required domains
- choose participating agents
- minimize execution cost
- define execution order
- estimate confidence

The Planner is forbidden from:

- modifying data
- calling tools
- updating memory
- bypassing services

---

## Multi-Agent Collaboration

Agents collaborate logically.

For the MVP they may execute sequentially inside a single backend process.

They are not required to run as independent services.

This distinction is fundamental.

Logical separation does not imply physical separation.

---

## Example

Guest says:

```
Necesito mover mi reserva para la próxima semana y quiero saber cuál sería la diferencia de precio.
```

Planner identifies:

Guest Domain

Reservation Domain

Revenue Domain

Required agents:

Guest Agent

↓

Revenue Agent

↓

Reservation Agent

↓

Aggregation

↓

Decision Validator

↓

Final response

---

## Context Budget

Unlike L2, L3 may retrieve information from multiple Brains.

However, retrieval remains selective.

Maximum recommendations:

Guest Brain

+

Property Brain

+

Revenue Brain

Never retrieve every available document.

Context must be assembled incrementally.

---

# 16. Level L4 — Strategic Intelligence

---

L4 never executes synchronously.

It exists for strategic optimization.

Examples:

Portfolio pricing

Season forecasting

Marketing optimization

Demand prediction

Revenue simulations

Competitor intelligence

Growth planning

These jobs execute asynchronously.

The user never waits.

---

## Execution Model

```
Scheduled Event

↓

Planner

↓

Long Context Assembly

↓

Strategic Agents

↓

Optimization

↓

Recommendation

↓

Owner Dashboard
```

---

L4 is not conversational.

It is analytical.

---

# 17. Adaptive Escalation Algorithm

Every request starts at L0.

Never above.

Escalation occurs only if necessary.

```
L0

↓

Need Natural Language?

↓

L1

↓

Need Context?

↓

L2

↓

Need Multiple Domains?

↓

L3

↓

Need Strategic Planning?

↓

L4
```

This minimizes latency while preserving reasoning quality.

---

# 18. Decision Validation Layer

One of the most important architectural rules.

AI proposes.

Business validates.

System executes.

The Decision Validator sits between reasoning and execution.

```
AI

↓

Decision Validator

↓

Domain Service

↓

Database
```

---

Responsibilities include:

semantic ambiguity detection

business policy enforcement

approval workflows

confidence thresholds

financial risk evaluation

irreversible action detection

---

Examples

Reservation cancellation

↓

Requires explicit confirmation

---

Refund

↓

Requires payment validation

---

Price reduction >15%

↓

Owner approval required

---

Deletion

↓

Always requires confirmation

---

# 19. Semantic Risk Matrix

| Action | Risk | Validation |
|----------|------|------------|
| FAQ | Low | None |
| Send message | Low | Automatic |
| Reservation update | Medium | Policy validation |
| Cancellation | High | Confirmation |
| Refund | Critical | Human approval |
| Delete data | Critical | Multi-step approval |

Risk determines validation.

Not AI confidence.

---

# 20. Token Budget

Token consumption is treated as an infrastructure resource.

Recommended limits:

L1

≤ 2k tokens

L2

≤ 6k tokens

L3

≤ 12k tokens

L4

Dynamic

Context compression is mandatory before exceeding limits.

---

# 21. Latency Budget

Every layer has a maximum latency.

Intent Detection

<20ms

Planner

<300ms

Context Retrieval

<300ms

LLM

<1500ms

Validation

<100ms

Total synchronous target

<3 seconds

---

# 22. Anti-Patterns

The following are forbidden:

Always invoking the Planner.

Always loading all Brains.

Always using multiple agents.

Long prompt chains.

Recursive agent conversations.

Unlimited context retrieval.

AI executing business rules.

n8n containing business logic.

---

# 23. Implementation Rules

The MVP implementation SHALL:

Execute every level inside one backend process.

Reuse one LLM provider.

Reuse prompts whenever possible.

Cache deterministic responses.

Prefer retrieval over reasoning.

Prefer software over prompts.

Reason only when reasoning creates measurable business value.

---

# 24. Success Metrics

Healthy production targets:

70–80% requests resolved at L0/L1

15–25% at L2

<5% at L3

<1% at L4

Median latency

<2 seconds

Average AI cost per interaction continuously decreasing over time.

---

# 25. Final Statement

The AI Execution Levels architecture transforms ATLAS from a system that always "thinks" into a system that thinks only when necessary.

This hierarchy ensures that intelligence scales with business value rather than with request volume, enabling low latency, low operational cost and predictable behavior while preserving the ability to perform sophisticated multi-agent reasoning whenever complexity truly requires it.
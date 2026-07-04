# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0030
# DOCUMENT    : AI Model Routing & Provider Strategy
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
# - RFC-0008
# - RFC-0010
# - ARCH-0012
# - ARCH-0014
# - ARCH-0020
#
# MUST reference this document.
#
# ============================================================================

# ARCH-0028

# AI MODEL ROUTING & PROVIDER STRATEGY

---

# 1. Purpose

This document defines how ATLAS selects, routes, monitors and replaces Artificial Intelligence models.

The objective is to maximize business value while minimizing latency, operational cost and vendor lock-in.

Model selection is treated as an architectural capability rather than an implementation detail.

---

# 2. Motivation

No single AI model is optimal for every task.

Some models provide:

- lower latency
- lower cost
- stronger reasoning
- better multilingual support
- better structured outputs
- larger context windows

ATLAS therefore separates **business capabilities** from **model providers**.

---

# 3. Core Principles

The following principles are immutable.

### Principle 1

ATLAS depends on AI capabilities.

Never on specific vendors.

---

### Principle 2

Models are replaceable.

Business logic remains unchanged.

---

### Principle 3

Provider selection is dynamic.

Never hardcoded.

---

### Principle 4

Routing decisions are deterministic.

---

### Principle 5

The cheapest model capable of solving the task is preferred.

---

# 4. AI Provider Abstraction

Every provider implements the same interface.

Example:

```
AI Provider

↓

Generate()

↓

Structured Output

↓

Tool Calls

↓

Embeddings

↓

Health

↓

Usage Metrics
```

Business components never communicate directly with providers.

---

# 5. Supported Providers

Examples:

OpenAI

Anthropic

Google Gemini

OpenRouter

Azure OpenAI

Self-hosted models

Future providers require only adapter implementation.

---

# 6. Model Router

Every request passes through the Model Router.

```
Request

↓

Execution Level

↓

Capability Requirements

↓

Provider Selection

↓

Model Selection

↓

Execution
```

No component selects models directly.

---

# 7. Routing Inputs

Routing decisions consider:

Execution Level

Expected latency

Expected cost

Language

Reasoning depth

Context length

Structured output requirements

Provider health

Organization preferences

---

# 8. Capability Matrix

Every model is classified according to:

- reasoning
- coding
- summarization
- translation
- extraction
- planning
- tool calling
- multilingual quality
- JSON reliability

Routing uses capabilities instead of names.

---

# 9. Latency Matrix

Models receive latency scores.

Example:

Fast

Medium

Slow

Latency-sensitive operations prioritize faster models.

---

# 10. Cost Matrix

Every model has:

Input token cost

Output token cost

Average request cost

Estimated business value

Routing optimizes cost continuously.

---

# 11. Context Matrix

Different models support different context windows.

The router considers:

Available context

Compression cost

Retrieval complexity

Long-context models are reserved for justified scenarios.

---

# 12. Execution Levels

Suggested routing:

L0

No model.

L1

Fast conversational model.

L2

Context-capable model.

L3

Reasoning model.

L4

Strategic reasoning model.

Execution levels define minimum capabilities.

---

# 13. Structured Output Support

Operations requiring deterministic JSON prioritize models with high structured-output reliability.

Examples:

Reservation updates

Workflow generation

Function calling

Business validations

---

# 14. Language Routing

ATLAS supports multilingual organizations.

Model selection considers:

Guest language

Owner language

Property language

Translation quality

---

# 15. Organization Policies

Organizations may configure:

Preferred provider

Maximum cost

Preferred region

Data residency

Fallback permissions

Routing respects organizational governance.

---

# 16. Provider Health

Every provider continuously reports:

Availability

Latency

Failure rate

Rate limits

Error frequency

Health influences routing.

---

# 17. Automatic Fallback

Example:

Primary provider unavailable

↓

Alternative provider selected

↓

Execution continues

Business services remain operational.

---

# 18. Vendor Independence

Business modules SHALL NOT:

Import provider SDKs directly.

Contain provider-specific prompts.

Depend on provider-specific APIs.

All provider logic belongs inside adapters.

---

# 19. Prompt Compatibility

Prompt templates remain provider-agnostic whenever possible.

Provider-specific optimizations are isolated behind adapter layers.

---

# 20. Local Models

Future versions may support:

Local LLMs

Edge inference

Private deployments

Hybrid cloud execution

No architectural changes are required.

---

# 21. AI Observability

Every execution records:

Provider

Model

Latency

Tokens

Cost

Execution Level

Success

Failure reason

Organization ID

These metrics enable routing optimization.

---

# 22. Anti-Patterns

Forbidden:

Hardcoding model names.

Business logic depending on providers.

Prompt duplication per provider.

Always selecting premium models.

Ignoring provider health.

Ignoring cost.

Ignoring latency.

---

# 23. Future Evolution

Planned capabilities include:

Automatic benchmarking

A/B model testing

Dynamic routing optimization

Multi-provider consensus

Specialized domain models

On-device inference

MCP-native provider integration

---

# 24. Success Metrics

The routing strategy is considered successful when:

- providers can be replaced without modifying business logic;
- routing minimizes cost while meeting SLA targets;
- failures trigger automatic recovery;
- latency remains predictable;
- organizations retain full control over provider preferences.

---

# 25. Final Statement

ATLAS treats AI providers as interchangeable infrastructure components.

Business capabilities, execution levels and architectural contracts remain stable regardless of the underlying model vendor.

This abstraction protects the platform from vendor lock-in, enables continuous optimization and ensures that future advances in AI models can be adopted with minimal engineering effort.
---

# ADDENDUM v1.1 — Routing por Execution Level (ADR-011)

Con la incorporación de Claude como proveedor primario, el routing se rige por la tabla de **ADR-011**:

| Execution Level | Proveedor por defecto |
|---|---|
| L1 | Modelo gratuito/open-source |
| L2, L3, L4 | Claude |

Regla dura: un modelo gratuito nunca es el último eslabón antes de una decisión L3/L4 visible al usuario (ver ARCH-0028 §7). Pendiente de definir operativamente: modelo gratuito específico para L1 (criterio: costo/latencia al momento de implementar, no arquitectónico).

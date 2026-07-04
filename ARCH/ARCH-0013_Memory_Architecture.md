# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0013
# DOCUMENT    : AI Memory Architecture
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Core Architecture
#
# REPLACES
#
# ARCH-0014_AI_MEMORY.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0002 AI Orchestrator
#
# ARCH-0021 AI Execution Levels
# ARCH-0025 MVP Physical Architecture
# ARCH-0027 AI Cost Optimization Strategy
# ARCH-0028 AI Model Routing & Provider Strategy
# ARCH-0029 Context Retrieval Strategy
#
# ============================================================================

# ARCH-0014

# AI MEMORY ARCHITECTURE

---

# 1. Purpose

This document defines the long-term memory architecture of ATLAS.

Its objective is to enable Artificial Intelligence to retrieve relevant historical knowledge without compromising latency, tenant isolation or operational consistency.

Memory is treated as a retrieval layer rather than persistent business state.

---

# 2. Architectural Principles

The memory subsystem follows five immutable principles.

### Principle 1

PostgreSQL is the operational source of truth.

Memory never replaces transactional data.

---

### Principle 2

Memory stores knowledge.

Not business state.

---

### Principle 3

Everything retrieved must remain attributable to its source.

---

### Principle 4

Memory retrieval is deterministic.

Reasoning begins only after retrieval completes.

---

### Principle 5

Memory is optimized for relevance.

Not completeness.

---

# 3. Memory Architecture

```
Business Data

↓

Knowledge Extraction

↓

Embeddings

↓

Vector Storage

↓

Semantic Retrieval

↓

Context Builder

↓

AI
```

Memory never bypasses the Context Builder.

---

# 4. Memory Domains

ATLAS defines three primary memory domains.

Guest Brain

Property Brain

Revenue Brain

Additional domains may be introduced without modifying the retrieval architecture.

---

# 5. Guest Brain

The Guest Brain stores durable guest knowledge.

Examples include:

- communication preferences;
- preferred language;
- accessibility needs;
- recurring requests;
- stay history summaries;
- loyalty indicators.

Personally identifiable information remains governed by privacy policies.

---

# 6. Property Brain

The Property Brain stores operational knowledge about each property.

Examples:

- amenities;
- house rules;
- maintenance history;
- equipment documentation;
- neighborhood information;
- check-in procedures.

Operational truth remains in PostgreSQL.

---

# 7. Revenue Brain

The Revenue Brain stores analytical knowledge.

Examples:

- historical pricing insights;
- demand patterns;
- occupancy trends;
- marketing performance;
- competitive observations;
- pricing recommendations.

Analytical knowledge is separate from operational pricing.

---

# 8. Knowledge Extraction

Knowledge enters memory through controlled extraction pipelines.

Sources include:

- conversations;
- operational records;
- documents;
- owner input;
- generated summaries.

Raw conversations are not stored indefinitely as memory.

---

# 9. Memory Lifecycle

```
Source Data

↓

Extraction

↓

Normalization

↓

Embedding

↓

Indexing

↓

Retrieval

↓

Refresh

↓

Retention
```

Each stage is observable.

---

# 10. Embeddings

Embeddings are generated only for information that benefits semantic retrieval.

Examples:

Documentation

Property descriptions

Conversation summaries

Guest preferences

Maintenance notes

Structured transactional data should not be embedded unnecessarily.

---

# 11. Memory Granularity

Memory units should be:

Atomic

Self-contained

Source-attributable

Version-aware

This improves retrieval precision.

---

# 12. Memory Versioning

Knowledge evolves.

New knowledge supersedes previous versions without deleting historical records.

Historical traceability is preserved.

---

# 13. Context Integration

Memory retrieval occurs exclusively through the Context Builder.

Behavior follows:

ARCH-0029

Direct memory access from AI components is prohibited.

---

# 14. Retrieval Policies

Memory retrieval considers:

- semantic relevance;
- entity relationships;
- recency;
- confidence;
- organizational boundaries.

Retrieval always respects tenant isolation.

---

# 15. Memory Budget

Execution Levels define retrieval limits.

Lower execution levels retrieve smaller memory windows.

Strategic execution levels may retrieve broader historical knowledge when justified.

Budget policies follow:

ARCH-0027

---

# 16. Compression

Before entering prompts:

Duplicate memories are merged.

Historical conversations are summarized.

Low-value memories are discarded.

Compression precedes truncation.

---

# 17. Security

Memory SHALL enforce:

Tenant isolation

Role permissions

Data minimization

Access auditing

Privacy compliance

Sensitive knowledge must never cross organizational boundaries.

---

# 18. Observability

Every retrieval records:

Memory Domain

Retrieved Entries

Retrieval Latency

Confidence Score

Embedding Version

Context Size

Execution Level

These metrics support optimization.

---

# 19. Retention

Different domains maintain different retention policies.

Examples:

Guest preferences

Long-term.

Maintenance notes

Medium-term.

Temporary operational observations

Short-term.

Retention follows business value.

---

# 20. Refresh Strategy

Knowledge is periodically re-evaluated.

Obsolete memories may be:

Archived

Merged

Re-embedded

Removed

Refresh policies prevent memory degradation.

---

# 21. Anti-Patterns

Forbidden:

Using memory as operational storage.

Embedding entire databases.

Unlimited conversation retention.

Memory without attribution.

Retrieving every memory.

Cross-tenant retrieval.

AI writing directly into memory.

---

# 22. Future Evolution

Future versions may introduce:

Graph memories

Cross-domain reasoning

Adaptive embeddings

Personalized retrieval

Multimodal memory

Hierarchical semantic indexes

These enhancements preserve the retrieval contracts defined here.

---

# 23. Success Metrics

The AI Memory Architecture is considered successful when:

- retrieval remains relevant and fast;
- memory never replaces transactional state;
- context quality improves over time;
- tenant isolation is preserved;
- storage costs remain sustainable.

---

# 24. Architectural Summary

The memory subsystem transforms historical knowledge into structured semantic assets that support AI reasoning without becoming a second operational database.

Operational truth remains deterministic.

Memory provides contextual intelligence.

---

# 25. Final Statement

The AI Memory Architecture enables ATLAS to learn from historical interactions while preserving the integrity of its operational model.

By separating transactional state from semantic knowledge and integrating all retrieval through the Context Builder, the platform delivers increasingly personalized and informed AI behavior without sacrificing consistency, performance or maintainability.
---

# ADDENDUM v2.1 — Decision Graph & Retention Policy

> Incorporado desde documentación previa mal etiquetada ("Doc 19 v2.0"). Fuente archivada en `_ARCHIVE/DUPLICATE_Memory_Content...`.

## Decision Graph

Además del resultado de cada decisión de agente, el sistema almacena:

- Qué información utilizó
- Qué reglas aplicó
- Qué agente decidió
- Qué herramientas consultó
- Qué resultado obtuvo

Con el tiempo se construye un **grafo de decisiones único por propiedad** — es uno de los principales moats de ATLAS (ver AES-00 Project DNA, Hipótesis de Moat #1).

## Política de Retención

| Memoria | Retención |
|---|---|
| Conversation Memory | Horas |
| Guest Brain | Indefinido |
| Property Brain | Indefinido |
| Growth Brain | Indefinido |
| Eventos (Event Bus) | Indefinido |
| Logs técnicos | Según configuración |

## Nota de costos

Memoria permanente en PostgreSQL. Embeddings solo sobre conocimiento útil — **no se vectoriza la base completa** (control de costo, ver ARCH-0027).

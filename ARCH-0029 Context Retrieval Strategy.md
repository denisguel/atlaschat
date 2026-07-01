# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0029
# DOCUMENT    : Context Retrieval Strategy
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
# This document defines the official Context Retrieval strategy used by every
# AI component inside ATLAS.
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

# ARCH-0029

# CONTEXT RETRIEVAL STRATEGY

---

# 1. Purpose

This document defines how ATLAS retrieves, ranks, compresses and assembles context for Artificial Intelligence.

The objective is to maximize response quality while minimizing latency, token consumption and retrieval cost.

Context retrieval is treated as a deterministic software problem—not an AI reasoning problem.

---

# 2. Motivation

Large Language Models do not possess operational memory.

Every response depends on the quality of the context provided.

Poor retrieval leads to hallucinations, higher costs and inconsistent business decisions.

The Context Builder is therefore a first-class architectural component.

---

# 3. Core Principles

The following principles are immutable.

### Principle 1

Never retrieve everything.

---

### Principle 2

Retrieve only what is necessary.

---

### Principle 3

Compression precedes truncation.

---

### Principle 4

Ranking precedes generation.

---

### Principle 5

Software retrieves.

AI reasons.

---

# 4. Context Pipeline

```
User Request

↓

Intent Detection

↓

Entity Resolution

↓

Retrieval Strategy

↓

Context Ranking

↓

Compression

↓

Assembly

↓

LLM
```

Every AI interaction follows this pipeline.

---

# 5. Context Sources

The Context Builder may retrieve information from:

- PostgreSQL
- Guest Brain
- Property Brain
- Revenue Brain
- Conversation History
- Knowledge Base
- Organization Policies
- External APIs
- Cached Results

Each source has a defined priority.

---

# 6. Context Categories

Context is classified into:

Operational Context

Conversational Context

Historical Context

Semantic Context

Strategic Context

Policy Context

Transient Context

Different requests require different combinations.

---

# 7. Retrieval Hierarchy

ATLAS follows this order:

Current Request

↓

Current Entity

↓

Current Conversation

↓

Operational Data

↓

Relevant Brain

↓

Knowledge Base

↓

Historical Context

↓

External Sources

Lower-priority sources are consulted only when necessary.

---

# 8. Entity Resolution

Before retrieval begins, the system identifies:

Organization

Property

Reservation

Guest

Task

Conversation

User

Every retrieval operation is entity-aware.

---

# 9. Retrieval Strategies

Supported strategies include:

Keyword Search

Semantic Search

Hybrid Search

Metadata Filtering

Temporal Search

Vector Similarity

Graph Traversal (future)

The Context Builder selects the appropriate strategy automatically.

---

# 10. Hybrid Retrieval

Hybrid retrieval combines:

Keyword relevance

+

Semantic similarity

+

Metadata filters

This improves precision over vector search alone.

---

# 11. Vector Search

Semantic retrieval uses pgvector.

Embeddings are generated for:

Knowledge articles

Guest memories

Property descriptions

Operational notes

Long documents

Only semantically indexed content participates in vector search.

---

# 12. Metadata Filtering

Vector similarity alone is insufficient.

Retrieval SHALL always consider metadata such as:

Organization ID

Property ID

Guest ID

Language

Document Type

Date Range

Access Permissions

Metadata filtering prevents irrelevant or unauthorized context.

---

# 13. Temporal Retrieval

Recent information is generally more valuable.

Examples:

Current reservation

↓

Current conversation

↓

Recent guest interactions

↓

Historical information

Temporal weighting influences ranking.

---

# 14. Context Ranking

Retrieved documents are ranked according to:

Semantic relevance

Recency

Business priority

Entity proximity

Confidence

Policy relevance

Only the highest-ranked items proceed.

---

# 15. Context Budget

Each execution level defines a maximum retrieval budget.

L1

Minimal.

L2

Single entity.

Limited Brain access.

L3

Multiple entities.

Cross-Brain retrieval.

L4

Adaptive retrieval.

Dynamic budget.

Unlimited retrieval is prohibited.

---

# 16. Context Compression

Before reaching the LLM:

Duplicate facts are removed.

Repeated conversations are summarized.

Long documents are condensed.

Low-value information is discarded.

Compression always preserves business-critical facts.

---

# 17. Conversation Windows

Conversation history is segmented into:

Current Session

Recent Sessions

Historical Sessions

Only relevant windows are retrieved.

Entire chat histories are never loaded by default.

---

# 18. Memory Windows

Brains maintain different memory horizons.

Guest Brain

Long-term preferences.

Property Brain

Operational knowledge.

Revenue Brain

Historical business intelligence.

Each Brain defines independent retention policies.

---

# 19. Context Assembly

The final context package includes:

System Instructions

Business Policies

Operational Data

Retrieved Knowledge

Conversation Summary

Current Request

The assembly order is deterministic.

---

# 20. Context Quality

Every assembled context is evaluated according to:

Completeness

Relevance

Consistency

Token Size

Source Diversity

Confidence

Poor-quality context is regenerated before AI execution.

---

# 21. Security

Context retrieval SHALL enforce:

Tenant isolation

Role permissions

Property ownership

Data residency policies

Access auditing

Unauthorized information must never enter the prompt.

---

# 22. Caching

Frequently retrieved context may be cached.

Examples:

House Rules

Wi-Fi Instructions

Amenities

Check-in Guide

Static Policies

Cache invalidation follows business events.

---

# 23. Anti-Patterns

Forbidden:

Loading entire databases.

Retrieving every Brain.

Ignoring metadata filters.

Unlimited conversation history.

Context assembly inside prompts.

Retrieval based solely on vector similarity.

Passing duplicated information to the LLM.

---

# 24. Success Metrics

The Context Retrieval Strategy is considered successful when:

- retrieval latency remains within SLA targets;
- token consumption is minimized;
- retrieved context is highly relevant;
- hallucinations caused by missing context are reduced;
- context assembly remains deterministic and auditable.

---

# 25. Final Statement

The Context Builder is one of the foundational components of ATLAS.

Rather than relying on increasingly larger prompts or more powerful language models, ATLAS prioritizes deterministic retrieval, intelligent ranking and efficient context assembly.

This approach enables faster responses, lower operational costs, improved factual accuracy and scalable AI performance while maintaining strict data isolation and business reliability.
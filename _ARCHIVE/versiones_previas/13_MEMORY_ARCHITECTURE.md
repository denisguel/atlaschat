# 13_MEMORY_ARCHITECTURE.md

---
id: ARCH-0013
title: Memory Architecture (Brains + Context System)
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: AI Systems
type: Architecture Specification
classification: Critical
related:
  - RFC-0003
  - ARCH-0012
  - RFC-0002
---

# 1. Purpose

This document defines the **implementation architecture of the Memory System** in ATLAS.

It describes how Brains (Guest, Property, Growth) are physically implemented, queried, updated, and integrated into AI orchestration.

---

# 2. Design Overview

The Memory System is built as a **multi-layer intelligence retrieval system**:

```
Business Events
      │
      ▼
Memory Ingestion Pipeline
      │
 ┌────┼───────────────┐
 ▼    ▼               ▼
Guest Property     Growth
Memory Store       Memory Store
      │
      ▼
Semantic Index Layer
      │
      ▼
Context Builder
      │
      ▼
AI Orchestrator
```

Memory is never directly accessed by agents.

---

# 3. Core Components

## 3.1 Memory Ingestion Pipeline

Responsible for transforming events into memory updates.

Inputs:

- domain events
- workflow events
- user interactions
- system signals

Output:

- structured memory entries
- embeddings
- summaries

---

## 3.2 Brain Stores

Each Brain is a logical separation of memory:

### Guest Brain Store
Stores:

- preferences
- behavior history
- communication style
- sentiment evolution
- lifetime value signals

---

### Property Brain Store
Stores:

- operational details
- rules
- amenities
- maintenance history
- local knowledge
- seasonal insights

---

### Growth Brain Store
Stores:

- marketing performance
- conversion data
- acquisition patterns
- channel attribution
- experiments

---

# 4. Storage Model

Memory is stored in a hybrid model:

## 4.1 Structured Layer (PostgreSQL)

- normalized facts
- metadata
- relationships

## 4.2 Semantic Layer (Vector DB)

- embeddings
- similarity search
- contextual retrieval

## 4.3 Event Layer (Event Store)

- immutable history
- traceability
- replay capability

---

# 5. Memory Write Flow

```
Event
  ↓
Extractor
  ↓
Classifier
  ↓
Summarizer
  ↓
Embedding Generator
  ↓
Brain Store Update
  ↓
Index Update
```

All writes are asynchronous.

---

# 6. Memory Read Flow

```
Query
  ↓
Context Builder
  ↓
Brain Selection
  ↓
Semantic Search
  ↓
Ranking Engine
  ↓
Context Assembly
  ↓
AI Orchestrator
```

Only relevant memory is returned.

---

# 7. Context Builder

The Context Builder is the main consumer of memory.

It applies:

- relevance scoring
- recency weighting
- confidence filtering
- tenant isolation
- compression rules

Output is a **Context Package**.

---

# 8. Memory Compression Strategy

To reduce token usage:

Raw data → Summary → Insight → Context snippet

Example:

```
Reservation history (100 events)
→ summarized behavior pattern
→ "Guest prefers late check-in and sea-view units"
```

---

# 9. Embedding Strategy

All memory entries are embedded using:

- standardized embedding model (provider-agnostic)
- versioned embedding schema
- tenant-aware namespaces

Embeddings support:

- semantic search
- clustering
- similarity scoring

---

# 10. Memory Versioning

Each memory entry includes:

- version_id
- source event
- timestamp
- confidence score
- update reason

Memory is never overwritten silently.

---

# 11. Memory Consistency Model

The system uses **eventual consistency**:

- events arrive
- memory updates asynchronously
- context reflects latest available state

This tradeoff is accepted for scalability.

---

# 12. Retrieval Prioritization

Memory is ranked using:

1. relevance
2. recency
3. confidence
4. business priority
5. frequency of access

---

# 13. Multi-Tenant Isolation

All memory layers enforce:

- organization_id isolation
- encrypted partitioning
- query-level filtering
- embedding namespace separation

Cross-tenant leakage is strictly prohibited.

---

# 14. AI Interaction Model

AI agents interact with memory ONLY via:

```
Orchestrator
   ↓
Context Builder
   ↓
Memory System
   ↓
Context Package
```

Agents cannot:

- query raw databases
- modify memory directly
- bypass ranking logic

---

# 15. Real-Time vs Historical Memory

## Real-Time Memory

- current session context
- active reservation state
- ongoing workflows

## Historical Memory

- past reservations
- long-term guest behavior
- marketing history
- seasonal patterns

Both are merged during context assembly.

---

# 16. Memory Expiration Policy

Not all memory is permanent:

- operational noise is compressed
- irrelevant data decays
- outdated patterns are downgraded
- critical facts persist indefinitely

---

# 17. Observability

Every memory operation logs:

- query_id
- brain type
- retrieval latency
- embedding version
- ranking scores
- returned context size

Memory performance is continuously optimized.

---

# 18. Security Model

Security rules:

- tenant isolation enforced at every layer
- sensitive fields encrypted
- access controlled via Orchestrator
- no direct external access to memory stores

---

# 19. Failure Handling

If memory system fails:

Fallback strategy:

1. use cached context
2. use event history
3. degrade to minimal context
4. proceed without memory

System must remain functional.

---

# 20. Anti-Patterns

Forbidden:

- direct agent memory writes
- unfiltered retrieval of all history
- cross-brain mixing without orchestration
- storing raw prompts as memory
- bypassing semantic ranking
- uncontrolled embedding generation

---

# 21. Future Evolution

Memory System is designed to evolve toward:

- fully autonomous memory consolidation
- cross-brain knowledge graphs
- predictive memory generation
- self-optimizing retrieval policies
- long-term behavioral modeling
- MCP-native memory providers

---

# 22. Success Criteria

The system is successful when:

- AI consistently retrieves relevant context
- token usage decreases over time
- personalization improves continuously
- memory remains explainable and auditable
- retrieval latency remains low at scale

---

# 23. Summary

The Memory Architecture defines ATLAS as a system with persistent, structured and evolving intelligence.

By separating storage, semantics and context assembly, it enables scalable long-term memory that improves AI reasoning without compromising performance, security or tenant isolation.
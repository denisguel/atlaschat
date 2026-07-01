# ADR-005_BRAIN_MEMORY_ARCHITECTURE.md

---
id: ADR-005
title: Adopt Persistent Brain-Based Memory Architecture
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Architecture
type: Architectural Decision Record
classification: Critical
related:
  - RFC-0001
  - ARCH-003
  - ARCH-006
  - ARCH-007
---

# 1. Context

Large Language Models are inherently stateless.

Without persistent memory:

- every conversation starts from zero
- personalization is impossible
- long-term optimization cannot exist
- business intelligence is fragmented
- AI repeatedly asks for information already known

ATLAS is designed as an operating system, not a chatbot.

Therefore, long-term memory is a core architectural requirement.

---

# 2. Decision

ATLAS adopts a **Brain-Based Memory Architecture**.

Persistent business knowledge is separated into specialized "Brains".

Brains are independent systems responsible for storing and retrieving contextual intelligence.

Agents consume memory.

They do not own it.

---

# 3. Decision Drivers

Primary motivations:

- persistent intelligence
- personalization
- reusable context
- lower token consumption
- faster AI reasoning
- explainability
- modular evolution

---

# 4. High-Level Architecture

```
                Event Bus
                    │
                    ▼
            Memory Update Pipeline
                    │
    ┌───────────────┼────────────────┐
    ▼               ▼                ▼
Guest Brain   Property Brain   Growth Brain
    │               │                │
    └───────────────┼────────────────┘
                    ▼
             Context Builder
                    │
                    ▼
            Orchestrator Agent
                    │
                    ▼
             Domain Agents
```

Brains never execute business logic.

Brains only provide context.

---

# 5. Brain Principles

Every Brain must be:

- persistent
- versioned
- queryable
- observable
- tenant-aware
- independently scalable

Brains are read-optimized.

Writes occur through events.

---

# 6. Guest Brain

Purpose:

Maintain long-term guest intelligence.

Examples:

- profile
- preferences
- communication style
- travel history
- reservation history
- satisfaction
- lifetime value
- sentiment
- behavioral patterns

Example query:

> "What should I know before answering this guest?"

---

# 7. Property Brain

Purpose:

Maintain operational property knowledge.

Examples:

- amenities
- rules
- manuals
- maintenance history
- seasonal notes
- owner preferences
- cleaning procedures
- local recommendations

Example query:

> "How should this property operate?"

---

# 8. Growth Brain

Purpose:

Maintain commercial intelligence.

Examples:

- marketing campaigns
- attribution
- conversion funnels
- lead sources
- SEO performance
- social performance
- pricing experiments
- booking behavior

Example query:

> "How can we increase bookings?"

---

# 9. Future Brains

Architecture allows additional Brains without redesign.

Potential future Brains:

- Revenue Brain
- Operations Brain
- Finance Brain
- Reputation Brain
- Vendor Brain
- Knowledge Brain
- Legal Brain
- Maintenance Brain

---

# 10. Update Model

Brains are never updated directly.

Flow:

```
Business Event

↓

Memory Pipeline

↓

Brain Update

↓

Indexed Context
```

Business logic never writes memory.

Events drive memory evolution.

---

# 11. Context Retrieval

Before every AI execution:

```
Intent

↓

Context Builder

↓

Relevant Brains

↓

Context Package

↓

AI Agent
```

Only relevant memory is retrieved.

---

# 12. Storage Model

Brains may combine:

Structured storage

+

Vector storage

+

Embeddings

+

Semantic indexes

Storage implementation is abstracted.

---

# 13. Memory Types

Each Brain stores:

## Facts

Objective information.

---

## Preferences

User-specific choices.

---

## Behaviors

Observed patterns.

---

## Summaries

AI-generated condensed knowledge.

---

## Relationships

Connections between entities.

---

# 14. Versioning

Every memory update is versioned.

Memory history is preserved.

Historical context may be reconstructed.

---

# 15. Explainability

Every retrieved memory must indicate:

- source
- confidence
- timestamp
- originating event

AI decisions become explainable.

---

# 16. Security

Memory respects:

- tenant isolation
- role permissions
- least privilege
- purpose limitation

No Brain exposes unrestricted information.

---

# 17. Scalability

Brains evolve independently.

Example:

Guest Brain

↓

Dedicated infrastructure.

Property Brain

↓

Different storage engine.

No redesign required.

---

# 18. Benefits

Brain architecture enables:

- long-term memory
- personalization
- reduced token usage
- reusable intelligence
- faster reasoning
- modular evolution
- explainable AI

---

# 19. Trade-Offs

Accepted complexity:

- memory synchronization
- indexing
- retrieval optimization
- version management

These costs are justified by persistent intelligence.

---

# 20. Alternatives Rejected

## Conversation History Only

Rejected.

History becomes too large.

Poor retrieval quality.

High token cost.

---

## Single Global Memory

Rejected.

Poor scalability.

Mixed responsibilities.

Low explainability.

---

## Agent-Owned Memory

Rejected.

Creates duplicated knowledge.

Difficult synchronization.

---

# 21. Future Evolution

Brains will evolve toward:

- graph-based knowledge
- autonomous memory consolidation
- semantic compression
- predictive memory retrieval
- cross-brain reasoning
- MCP-native memory providers

---

# 22. Success Criteria

This ADR is considered successful when:

- AI never depends solely on chat history;
- memory persists across conversations;
- context retrieval is selective;
- Brains remain independent;
- updates are event-driven;
- AI responses become increasingly personalized over time.

---

# 23. Status

Accepted.

The Brain Memory Architecture becomes the official long-term memory model for ATLAS.

Persistent intelligence is separated into specialized Brains that continuously evolve through business events, providing contextual knowledge to AI while remaining independent of agents and business logic.
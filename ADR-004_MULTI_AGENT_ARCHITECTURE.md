# ADR-004_MULTI_AGENT_ARCHITECTURE.md

---
id: ADR-004
title: Adopt Multi-Agent Architecture Instead of a Single General AI
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Architecture
type: Architectural Decision Record
classification: Critical
related:
  - RFC-0001
  - ARCH-002
  - ARCH-003
  - ARCH-006
---

# 1. Context

One of the earliest architectural decisions for ATLAS is determining how artificial intelligence should be structured.

Possible approaches include:

- One general-purpose LLM
- One AI per feature
- Rule-based automation
- Multi-Agent Architecture

ATLAS aims to become an AI-native operating system rather than an application with AI features.

Therefore, intelligence must be modular, scalable, observable and independently evolvable.

---

# 2. Decision

ATLAS adopts a **Multi-Agent Architecture** coordinated by a central **Orchestrator Agent**.

Each agent owns a specific business domain.

Agents never own infrastructure.

Agents never own persistence.

Agents reason.

Services execute.

---

# 3. Decision Drivers

Primary motivations:

- specialization
- explainability
- modular evolution
- easier testing
- lower hallucination risk
- context isolation
- independent optimization
- future autonomous collaboration

---

# 4. High-Level Architecture

```
                    User
                      │
                      ▼
            Conversation Layer
                      │
                      ▼
             Orchestrator Agent
                      │
      ┌───────────────┼────────────────┐
      ▼               ▼                ▼
Guest Relations   Revenue Agent   Operations Agent
      │               │                │
      ├───────────────┼────────────────┤
      ▼               ▼                ▼
 Content Agent   Conversion Agent   Future Agents
                      │
                      ▼
              Domain Services Layer
                      │
                      ▼
                 Event Bus
                      │
                      ▼
                 Infrastructure
```

---

# 5. Responsibilities

## Orchestrator Agent

Responsibilities:

- interpret user intent
- determine execution strategy
- build execution plan
- retrieve context
- select domain agents
- coordinate multi-agent tasks
- merge results
- supervise execution

The Orchestrator never executes business operations directly.

---

## Domain Agents

Each domain agent owns one business capability.

Examples:

### Guest Relations Agent

Owns:

- guest communication
- personalization
- hospitality recommendations

---

### Revenue Agent

Owns:

- pricing
- occupancy optimization
- revenue recommendations

---

### Operations Agent

Owns:

- maintenance
- housekeeping
- operational planning
- check-in orchestration

---

### Conversion Agent

Owns:

- reservation optimization
- lead qualification
- sales recommendations

---

### Content Agent

Owns:

- listings
- SEO
- Instagram
- WhatsApp campaigns
- marketing assets

---

# 6. Agent Principles

Every agent must be:

- stateless
- deterministic when possible
- domain-focused
- tool-restricted
- observable
- replaceable

---

# 7. Context Model

Agents never load the entire system state.

Instead they receive:

```
Context Package

↓

Relevant Brain Data

↓

Business Constraints

↓

Recent Events

↓

Current Intent
```

Only the required information is exposed.

---

# 8. Communication Model

Agents never communicate directly.

Allowed communication:

```
Agent

↓

Orchestrator

↓

Agent
```

or

```
Agent

↓

Event Bus

↓

Subscriber
```

Direct peer-to-peer communication is forbidden.

---

# 9. Memory Model

Agents do not own memory.

Memory belongs to dedicated Brain systems.

Examples:

- Guest Brain
- Property Brain
- Growth Brain

Future:

- Revenue Brain
- Operations Brain

---

# 10. Tool Model

Agents do not have unrestricted access.

Each agent receives only the tools required for its domain.

Example:

Revenue Agent

Allowed:

- pricing
- occupancy
- calendar

Forbidden:

- delete property
- modify users
- billing administration

Least privilege applies to AI.

---

# 11. Planning Model

The Orchestrator generates execution plans.

Example:

```
Intent

↓

Retrieve Context

↓

Revenue Agent

↓

Pricing Recommendation

↓

Reservation Service

↓

Emit Events

↓

Generate Response
```

Complex tasks may involve multiple agents.

---

# 12. Failure Strategy

If one agent fails:

- retry
- degrade gracefully
- escalate to user if required

Other agents continue operating.

The platform must tolerate partial AI failures.

---

# 13. Scalability

Each agent scales independently.

Possible future deployment:

- Revenue Cluster
- Operations Cluster
- Content Cluster
- Guest Cluster

No architectural redesign required.

---

# 14. Observability

Every agent execution must record:

- execution_id
- prompt version
- context version
- tools invoked
- latency
- model used
- token usage
- output
- confidence (if available)

---

# 15. Security

Agents:

- cannot bypass authorization
- cannot call unauthorized tools
- cannot access unrestricted context
- cannot cross tenant boundaries

Every action is validated before execution.

---

# 16. Benefits

This architecture enables:

- specialization
- modular evolution
- better prompt engineering
- lower token usage
- lower costs
- easier debugging
- explainability
- independent testing

---

# 17. Drawbacks

Accepted trade-offs:

- orchestration complexity
- additional coordination
- more infrastructure components
- more observability requirements

These costs are justified by long-term scalability.

---

# 18. Alternatives Rejected

## Single General Agent

Rejected.

Reasons:

- oversized prompts
- poor specialization
- expensive context windows
- difficult testing

---

## Rule-Based Automation

Rejected.

Reasons:

- inflexible
- poor adaptability
- high maintenance

---

## AI Embedded Inside Services

Rejected.

Reasons:

- mixes intelligence with execution
- difficult to audit
- impossible to evolve independently

---

# 19. Future Evolution

The architecture is designed to support:

- autonomous agent collaboration
- dynamic agent creation
- agent marketplaces
- model-per-agent optimization
- MCP-native agents
- self-improving orchestration
- distributed reasoning

The number of agents should be allowed to grow without changing the platform architecture.

---

# 20. Success Criteria

This ADR is considered successful when:

- each business domain has a clearly defined agent;
- no agent owns unrelated responsibilities;
- the Orchestrator coordinates all complex workflows;
- business logic remains deterministic in services;
- AI remains modular and independently evolvable.

---

# 21. Status

Accepted.

Multi-Agent Architecture is the canonical intelligence model for ATLAS.

Artificial intelligence is decomposed into specialized reasoning units coordinated by an Orchestrator, enabling scalability, maintainability and future autonomous capabilities without architectural redesign.
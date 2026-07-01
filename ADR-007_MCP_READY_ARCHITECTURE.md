# ADR-007_MCP_READY_ARCHITECTURE.md

---
id: ADR-007
title: Design ATLAS as MCP-Ready from Day One
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Architecture
type: Architectural Decision Record
classification: Strategic
related:
  - RFC-0001
  - ARCH-002
  - ARCH-005
  - ADR-004
---

# 1. Context

The AI ecosystem is rapidly converging toward standardized protocols for tool execution and context exchange.

The **Model Context Protocol (MCP)** is emerging as the de facto standard for allowing AI systems to discover, invoke and compose external capabilities.

Although the MVP will not depend on MCP, the architecture must avoid future rewrites.

---

# 2. Decision

ATLAS will be designed as **MCP-Ready**.

Internal services will expose clear capability boundaries that can later be mapped to MCP tools without changing business logic.

MCP is treated as an architectural compatibility target, not an implementation dependency.

---

# 3. Decision Drivers

Primary motivations:

- future interoperability
- vendor independence
- standardized tool interfaces
- AI ecosystem compatibility
- reusable capabilities
- long-term extensibility

---

# 4. Architectural Principle

Business capabilities are exposed as **Tools**, not as AI prompts.

AI reasons.

Tools execute.

This separation already exists inside the architecture and naturally aligns with MCP.

---

# 5. Current Architecture

```
AI Agent

↓

Domain Service

↓

Business Logic

↓

Database
```

---

# 6. Future MCP Architecture

```
AI Agent

↓

MCP Tool

↓

Domain Service

↓

Business Logic

↓

Database
```

Only a thin compatibility layer should be required.

---

# 7. Capability Design

Every executable capability should eventually become an MCP Tool.

Examples:

Reservation Tool

Functions:

- create reservation
- cancel reservation
- update reservation

---

Pricing Tool

Functions:

- calculate price
- suggest price
- explain pricing

---

Guest Tool

Functions:

- retrieve guest
- update preferences
- summarize history

---

Content Tool

Functions:

- generate listing
- improve SEO
- create Instagram caption

---

Analytics Tool

Functions:

- occupancy report
- revenue report
- conversion report

---

# 8. Tool Principles

Every tool must be:

- deterministic
- observable
- authenticated
- authorized
- versioned
- documented
- independently testable

---

# 9. AI Responsibilities

AI decides:

- which tool to use
- when to use it
- how to combine results

AI never performs database mutations directly.

---

# 10. Tool Contracts

Each tool should define:

Input Schema

Output Schema

Permission Requirements

Error Model

Version

Expected Side Effects

These contracts are independent of the AI model.

---

# 11. Context Separation

MCP compatibility requires clear separation between:

Reasoning

↓

Context

↓

Execution

↓

Persistence

ATLAS already follows this model.

---

# 12. Security

Every future MCP Tool must enforce:

- tenant isolation
- authentication
- authorization
- input validation
- audit logging

Tool execution cannot bypass platform security.

---

# 13. Observability

Every invocation records:

- tool name
- version
- execution time
- actor
- tenant
- parameters
- outcome

Tool execution must be fully traceable.

---

# 14. Multi-Agent Compatibility

Agents interact with tools through the Orchestrator.

```
Owner Request

↓

Orchestrator

↓

Revenue Agent

↓

Pricing Tool

↓

Pricing Service
```

Tools remain reusable across agents.

---

# 15. Migration Strategy

Current MVP:

AI

↓

Internal Services

---

Future:

AI

↓

MCP Adapter

↓

Internal Services

---

Long Term:

External MCP Ecosystem

↓

ATLAS MCP Server

↓

Internal Services

Business logic remains unchanged.

---

# 16. Benefits

The architecture gains:

- protocol compatibility
- reusable tools
- vendor independence
- standardized execution
- easier integrations
- future AI interoperability

---

# 17. Trade-Offs

Current cost:

- stricter service boundaries
- explicit contracts
- additional documentation

These costs are accepted because they reduce future migration effort.

---

# 18. Alternatives Rejected

## Direct LLM Function Calls

Rejected.

Reasons:

- provider-specific
- weak portability
- changing APIs

---

## AI Embedded Inside Services

Rejected.

Reasons:

- poor separation
- impossible MCP mapping
- tightly coupled execution

---

## Monolithic Tool Layer

Rejected.

Reasons:

- poor modularity
- difficult versioning
- weak ownership

---

# 19. Future Evolution

Potential future capabilities:

- native MCP Server
- external MCP Clients
- partner tool ecosystem
- third-party hospitality tools
- marketplace of capabilities
- autonomous cross-platform agents

---

# 20. Success Criteria

This ADR is considered successful when:

- every business capability can be exposed as a tool;
- no service redesign is required for MCP adoption;
- AI remains independent of execution implementation;
- tool contracts remain stable across providers.

---

# 21. Status

Accepted.

ATLAS is officially designed as an MCP-Ready architecture.

Although the MVP does not require MCP, every service boundary, capability definition and execution contract must preserve a migration path toward native MCP compatibility without architectural redesign.
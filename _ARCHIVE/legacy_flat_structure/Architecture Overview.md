---

id: ARCH-000
title: Architecture Overview
version: 1.0.0
status: canonical

owner: Architecture

type: architecture

depends_on:

* PRODUCT-001
* PRODUCT-002
* PRODUCT-003

implemented_by: []

supersedes: []

## last_updated: 2026-06-29

# Architecture Overview

## Purpose

This directory contains the canonical engineering specification for the architecture of ATLAS.

Every architectural decision, system component, data flow, integration and infrastructure rule originates here.

The goal of this specification is **not** to describe the software.

The goal is to define **how the software must be built**.

Any implementation that diverges from these documents should be considered a different architecture.

---

# Scope

This section specifies:

* System Architecture
* Software Architecture
* Service Boundaries
* Domain Boundaries
* Communication Patterns
* Infrastructure
* AI Architecture
* Event Model
* Data Flow
* Security Model
* Scalability Model
* Deployment Strategy

The following subjects are intentionally excluded because they are specified elsewhere:

* Product requirements
* Database schema
* Prompt engineering
* Agent behaviour
* User Interface
* Business metrics

---

# Architectural Philosophy

ATLAS is designed as an **AI-Native Operating System**.

This distinction is fundamental.

Traditional software follows this flow:

```
User
↓

Application

↓

Database
```

AI-enabled software generally inserts an LLM between the UI and the backend:

```
User

↓

UI

↓

LLM

↓

Backend
```

ATLAS follows a different model:

```
User

↓

Orchestrator

↓

Specialized Agents

↓

Business Services

↓

Infrastructure
```

The user interacts with the AI.

The AI operates the software.

The software becomes an execution engine.

---

# Architectural Goals

The architecture must satisfy the following goals.

## G1

Operate entirely through conversational interfaces.

Primary channel:

WhatsApp.

Secondary channels:

Web

Email

Future voice interfaces.

---

## G2

Support autonomous execution.

Agents must be capable of executing complete workflows without human intervention whenever permitted.

---

## G3

Separate intelligence from execution.

Decision making belongs to agents.

Execution belongs to services.

Persistence belongs to databases.

Automation belongs to workflows.

Presentation belongs to UI.

---

## G4

Maintain strict modularity.

Every major capability must evolve independently.

No module may directly depend on another module's internal implementation.

Only published contracts.

---

## G5

Be event-driven.

The platform communicates through domain events rather than direct orchestration whenever possible.

---

## G6

Support horizontal growth.

Growth dimensions include:

* Properties
* Owners
* Organizations
* Countries
* Languages
* AI providers
* Communication channels

No redesign should be required.

---

# System Layers

The platform is divided into independent layers.

```
Presentation Layer

↓

Conversation Layer

↓

AI Layer

↓

Application Layer

↓

Domain Layer

↓

Infrastructure Layer

↓

Persistence Layer
```

Each layer has clearly defined responsibilities.

No layer may bypass another.

---

# Architecture Principles

The complete list is defined in:

```
01_ARCHITECTURAL_PRINCIPLES.md
```

This document only introduces the concepts.

Principles include:

* AI Native
* Event First
* API First
* Memory First
* Stateless Services
* Human in the Loop
* Modular Domains
* Security by Design
* Observability by Default

---

# Major Components

The architecture consists of the following macro components.

## Presentation

* Web Application
* Admin Dashboard
* Booking Engine
* Mobile Responsive UI

---

## Conversation

* WhatsApp
* Email
* Future Voice
* Future Instagram

---

## AI

* Orchestrator
* Guest Agent
* Revenue Agent
* Operations Agent
* Content Agent
* Conversion Agent

---

## Memory

* Guest Brain
* Property Brain
* Growth Brain

Future:

* Revenue Brain
* Operations Brain

---

## Services

Reservation Service

Property Service

Guest Service

Pricing Service

Messaging Service

Content Service

Analytics Service

Workflow Service

Authentication Service

---

## Infrastructure

Supabase

PostgreSQL

Storage

Edge Functions

Realtime

Authentication

---

## Automation

n8n

Future Workflow Engine abstraction.

---

# Architectural Documentation Map

```
02_ARCHITECTURE/

README.md

01_ARCHITECTURAL_PRINCIPLES.md

02_HIGH_LEVEL_ARCHITECTURE.md

03_SYSTEM_COMPONENTS.md

04_DOMAIN_MODEL.md

05_SERVICES.md

06_EVENT_BUS.md

07_DATA_FLOW.md

08_API_GATEWAY.md

09_SECURITY_MODEL.md

10_SCALABILITY.md

ADR/
```

Documents should be read sequentially.

Later documents assume knowledge established by previous documents.

---

# Decision Records

Architectural decisions are never embedded inside implementation documents.

Every important decision receives an ADR.

Example:

```
ADR-001

Why Supabase?

ADR-002

Why Event Driven?

ADR-003

Why WhatsApp First?

ADR-004

Why Multi-Agent?

ADR-005

Why PostgreSQL instead of MongoDB?
```

This preserves historical context and prevents undocumented architectural drift.

---

# Engineering Standards

Every document in this repository follows the same structure:

1. Purpose
2. Scope
3. Requirements
4. Constraints
5. Architecture
6. Interfaces
7. Contracts
8. Failure Modes
9. Security Considerations
10. Future Evolution

Consistency is mandatory.

---

# Non Functional Requirements

The architecture must prioritize:

* Reliability
* Simplicity
* Low operational cost
* Extensibility
* Testability
* Explainability
* Observability

Performance optimizations are introduced only after measurement.

Premature optimization is considered an anti-pattern.

---

# Reading Order

Recommended order:

1. Product
2. Architecture
3. Database
4. AI
5. Agents
6. Brains
7. Backend
8. Frontend
9. APIs
10. Workflows
11. Deployment

Skipping sections is discouraged.

---

# Definition of Done

The architecture is considered complete when:

* every service has a contract;
* every domain has clear boundaries;
* every event is documented;
* every integration is specified;
* every agent interaction is defined;
* every non-functional requirement is traceable;
* every architectural decision has an ADR.

Until then, the architecture remains a living specification.

---

# Change Management

All future architectural modifications must:

* preserve backward compatibility whenever possible;
* include an ADR if they affect core design;
* update dependent documents;
* increment semantic versioning.

No undocumented architectural changes are permitted.

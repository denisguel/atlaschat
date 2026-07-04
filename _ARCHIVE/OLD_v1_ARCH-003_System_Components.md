# 03_SYSTEM_COMPONENTS.md

---
id: ARCH-003
title: System Components
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
---

# 1. Purpose

This document defines all major system components in ATLAS and their responsibilities.

It establishes **what exists in the system**, independent of implementation details.

---

# 2. Component Design Principles

All components must follow:

- Single responsibility
- Clear input/output contracts
- Stateless behavior where applicable
- Event-driven interaction
- No cross-layer violations
- AI/Service separation

---

# 3. Macro Components Overview

ATLAS is composed of 6 macro subsystems:

Conversation System
AI Orchestration System
Domain Agents System
Domain Services System
Memory System (Brains)
Infrastructure & Event System

---

# 4. Conversation System

## 4.1 Purpose

Handles all external communication channels.

---

## 4.2 Channels

- WhatsApp (primary)
- Web App (dashboard + booking engine)
- Email
- Future: Voice, OTA chat, Instagram

---

## 4.3 Responsibilities

- receive user input
- normalize messages
- detect intent type
- forward to AI Orchestrator
- render responses

---

## 4.4 Constraints

- no business logic
- no decision-making
- no data persistence
- no direct service calls

---

# 5. AI Orchestration System

## 5.1 Purpose

Central intelligence coordination layer.

---

## 5.2 Core Components

### Orchestrator Agent

- receives all intents
- selects domain agent
- builds execution plan

### Context Builder

- retrieves Brain data
- constructs AI context payload

### Routing Engine

- maps intent → agent → service

---

## 5.3 Responsibilities

- interpret user intent
- select agent
- retrieve memory
- enforce policies
- define execution path

---

## 5.4 Constraints

- no persistence
- no direct DB access
- no long-term state storage

---

# 6. Domain Agents System

## 6.1 Purpose

Specialized AI workers responsible for domain reasoning.

---

## 6.2 Agents

### Guest Relations Agent
- messaging
- support
- personalization

### Revenue Agent
- pricing strategy
- occupancy optimization
- yield suggestions

### Operations Agent
- check-in/out workflows
- cleaning coordination
- maintenance logic

### Content Agent
- marketing content
- listings optimization
- social posts

### Conversion Agent
- booking optimization
- inquiry handling
- persuasion flows

---

## 6.3 Rules

- agents are stateless
- agents do not execute directly
- agents only produce structured outputs
- all actions go through services

---

# 7. Domain Services System

## 7.1 Purpose

Deterministic execution layer.

---

## 7.2 Services

### Reservation Service
- create / update / cancel bookings

### Property Service
- manage property metadata

### Pricing Service
- pricing rules execution

### Messaging Service
- send/receive messages

### Content Service
- store and publish content

### Analytics Service
- metrics aggregation

### Workflow Service
- trigger automation flows

### Auth Service
- identity and access control

---

## 7.3 Rules

- no AI logic
- strict validation
- deterministic behavior
- idempotent operations

---

# 8. Memory System (Brains)

## 8.1 Purpose

Persistent contextual intelligence layer.

---

## 8.2 Components

### Guest Brain
- guest history
- preferences
- behavior patterns

### Property Brain
- property rules
- amenities
- operational data

### Growth Brain
- marketing performance
- campaigns
- acquisition data

---

## 8.3 Rules

- read-heavy for AI
- write via events only
- structured + unstructured data allowed
- versioned memory updates

---

# 9. Infrastructure & Event System

## 9.1 Event Bus

Central communication backbone.

Events are:

- immutable
- append-only
- time-ordered

---

## 9.2 Event Types

- ReservationCreated
- ReservationCancelled
- GuestMessageReceived
- PriceUpdated
- WorkflowTriggered
- CheckInCompleted

---

## 9.3 Infrastructure Components

- Supabase (PostgreSQL + Auth + Storage)
- Edge Functions
- n8n workflows
- External APIs (OTAs, payments)

---

## 9.4 Responsibilities

- data persistence
- event storage
- async processing
- external integrations

---

# 10. Component Interaction Model

## 10.1 Standard Flow


Conversation
→ Orchestrator
→ Agent
→ Service
→ Event Bus
→ Database


---

## 10.2 Event Flow


Service
→ Event Bus
→ Subscribers (Agents / Workflows)
→ Side Effects


---

# 11. Component Boundaries

Strict isolation rules:

- Conversation ≠ AI
- AI ≠ Services
- Services ≠ Memory
- Memory ≠ Execution
- Infrastructure ≠ Business Logic

---

# 12. Failure Modes

## AI Layer
- fallback to rule-based routing

## Services
- retry + idempotency

## Events
- dead letter queue

## Conversation
- async recovery

---

# 13. Scalability Constraints

Each component must scale independently:

- AI scaling (requests)
- Services scaling (transactions)
- Events scaling (throughput)
- Memory scaling (reads)
- Conversation scaling (channels)

---

# 14. Future Extensions

- MCP execution layer
- distributed agent network
- real-time pricing engine
- predictive operations system
- cross-property intelligence mesh

---

# 15. Summary

ATLAS is composed of clearly separated systems:

- Conversation captures intent
- AI interprets intent
- Agents decide actions
- Services execute logic
- Events synchronize state
- Memory provides context

This separation is what enables ATLAS to operate as an AI-native hospitality operating system.
# ADR-003_WHATSAPP_FIRST_ARCHITECTURE.md

---
id: ADR-003
title: Adopt WhatsApp as the Primary User Interface
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Architecture
type: Architectural Decision Record
classification: Strategic
related:
  - RFC-0001
  - ARCH-002
  - ARCH-007
---

# 1. Context

Hospitality software traditionally assumes that users operate through dashboards.

Independent property owners in LATAM rarely spend their day inside a PMS.

Instead, they already operate from:

- WhatsApp
- Phone calls
- Instagram
- Email

The software should adapt to existing user behavior instead of forcing new workflows.

---

# 2. Decision

ATLAS adopts **WhatsApp as the Primary Interface**.

The web application exists primarily for:

- configuration
- administration
- reporting
- advanced operations

Daily operation is expected to happen through WhatsApp.

---

# 3. Decision Drivers

Primary drivers:

- highest adoption in LATAM
- lowest learning curve
- mobile-first operation
- immediate user familiarity
- continuous engagement
- reduced onboarding time

---

# 4. Architectural Principle

The conversation **is the interface**.

The dashboard is an administrative tool.

This reverses the architecture of traditional PMS platforms.

---

# 5. User Experience Model

Traditional PMS

```
User
↓

Dashboard

↓

Business Logic

↓

Database
```

ATLAS

```
User

↓

WhatsApp

↓

AI Orchestrator

↓

Business Services

↓

Database
```

The conversation becomes the operating system.

---

# 6. Supported Operations

The owner should be able to perform, through WhatsApp:

## Reservations

- create
- modify
- cancel
- search

---

## Guests

- search guest
- view history
- add notes

---

## Properties

- check availability
- block dates
- update pricing

---

## Operations

- check-ins
- check-outs
- maintenance requests
- cleaning status

---

## Analytics

- occupancy
- revenue
- reservations
- conversion

---

## Content

- generate Instagram posts
- generate listing descriptions
- create WhatsApp campaigns

---

# 7. Dashboard Responsibilities

The web dashboard is reserved for:

- onboarding
- account settings
- property configuration
- integrations
- analytics visualization
- billing
- advanced administration

It is not the primary operating interface.

---

# 8. AI Responsibilities

The AI layer is responsible for:

- interpreting natural language
- resolving ambiguity
- asking clarifying questions
- selecting workflows
- executing actions
- explaining results

The UI should remain intentionally simple.

---

# 9. Conversation Principles

Conversations must be:

- contextual
- persistent
- personalized
- proactive
- explainable

The system should remember previous interactions whenever appropriate.

---

# 10. Business Benefits

Expected outcomes:

- lower onboarding friction
- higher adoption
- increased daily usage
- reduced training costs
- faster task completion

---

# 11. Technical Benefits

Architecture advantages:

- channel independence
- AI-native interaction
- reusable workflows
- unified intent processing

Future channels require only a new Conversation Adapter.

---

# 12. Conversation Adapters

Current:

- WhatsApp

Future:

- Web Chat
- Instagram
- Facebook Messenger
- Voice
- Telegram
- Email
- Apple Messages
- SMS

All adapters normalize requests into a common intent model.

---

# 13. Failure Strategy

If WhatsApp becomes unavailable:

Fallback channels:

- Web Dashboard
- Email
- Future Web Chat

Business continuity must be preserved.

---

# 14. Security Considerations

Conversations must:

- authenticate users
- verify tenant ownership
- avoid exposing confidential information
- require confirmation for destructive actions

High-risk operations may require explicit approval.

---

# 15. AI Constraints

AI must never:

- perform irreversible operations without authorization
- expose internal prompts
- leak tenant information
- execute tools without permission

---

# 16. Anti-Patterns

Forbidden:

- dashboard-only workflows
- UI-specific business logic
- duplicated workflows between dashboard and WhatsApp
- channel-specific business rules
- separate AI implementations per channel

---

# 17. Long-Term Vision

WhatsApp is the first interface.

It is **not** the only interface.

The architecture must support unlimited conversational channels without changing business logic.

Conversation Adapters remain thin translation layers.

---

# 18. Success Metrics

The decision is successful if:

- >80% of daily owner operations occur through WhatsApp
- average task completion time decreases
- onboarding time decreases
- dashboard usage shifts toward administration instead of operations

---

# 19. Future Evolution

Future versions may support:

- multimodal conversations
- voice assistants
- image understanding
- document understanding
- autonomous proactive messaging
- AI-initiated workflows

---

# 20. Status

Accepted.

WhatsApp becomes the canonical operational interface for ATLAS.

The dashboard complements the conversational experience but does not replace it.

This decision fundamentally differentiates ATLAS from traditional hospitality management systems.
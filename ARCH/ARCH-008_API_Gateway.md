# 08_API_GATEWAY.md

---
id: ARCH-008
title: API Gateway Architecture
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
---

# 1. Purpose

This document defines the API Gateway layer of ATLAS.

The API Gateway is the unified entry point for all non-conversational system interactions (web, integrations, internal tools).

It enforces routing, security, and protocol normalization.

---

# 2. Core Principle

> All external system access must pass through a controlled, observable and policy-enforced gateway.

No service is exposed directly to external consumers.

---

# 3. Position in Architecture


Clients (Web / Integrations / Admin)
↓
API Gateway
↓
AI Orchestrator (if AI intent required)
↓
Domain Services
↓
Event Bus
↓
Persistence Layer


---

# 4. Responsibilities

The API Gateway is responsible for:

- request routing
- authentication enforcement
- rate limiting
- request validation
- protocol normalization
- tenant resolution
- logging and tracing
- version control of APIs

---

# 5. Supported Clients

## 5.1 Web Application

- dashboard
- property management
- reservation management
- analytics

---

## 5.2 External Integrations

- OTA platforms (Airbnb, Booking.com future sync)
- payment providers
- messaging services
- partner APIs

---

## 5.3 Internal Services

- admin tools
- debugging tools
- analytics pipelines

---

# 6. Request Flow


Client Request
↓
API Gateway
↓
Auth Validation
↓
Tenant Resolution
↓
Route Resolution
↓
Service / Orchestrator
↓
Response Aggregation


---

# 7. Authentication Model

## 7.1 Method

- JWT-based authentication (Supabase Auth)

---

## 7.2 Authorization

Role-based access control:

- Owner
- Admin
- Staff
- System Agent
- Integration Service

---

## 7.3 Tenant Isolation

Every request must include:

- tenant_id

No request is valid without tenant context resolution.

---

# 8. Routing Model

The gateway routes requests based on:

- endpoint type
- version
- tenant context
- authentication role
- payload type

---

## 8.1 Routing Types

### Direct Service Route


/api/reservations → Reservation Service


---

### AI Routed Flow


/api/ai/message → Orchestrator Agent


---

### Event Trigger Route


/api/events → Event Bus


---

# 9. API Design Principles

- REST-first design
- JSON as standard format
- versioned endpoints (/v1, /v2)
- backward compatibility required
- idempotent operations for writes
- explicit error contracts

---

# 10. Rate Limiting

Policies:

- per tenant limits
- per user limits
- per endpoint limits

AI endpoints have stricter throttling.

---

# 11. Error Handling

Standardized error format:


{
error_code,
message,
context,
trace_id
}


---

# 12. Logging & Observability

Every request generates:

- request_id
- tenant_id
- user_id
- endpoint
- latency
- response status

All logs are correlated with event system.

---

# 13. Security Constraints

- no direct DB access
- no bypass of gateway allowed
- all external calls validated
- payload sanitization required
- AI endpoints sandboxed

---

# 14. AI Integration Layer

Some requests are routed to AI layer:

Examples:

- availability queries
- pricing suggestions
- guest messaging
- content generation

Flow:


API Gateway
↓
AI Orchestrator
↓
Domain Services


---

# 15. Caching Strategy

- read-heavy endpoints cached
- AI responses optionally cached (short TTL)
- property metadata cached
- invalidation via events

---

# 16. Failure Modes

## Service Down

→ fallback response
→ retry queue

## AI Failure

→ deterministic fallback logic

## Auth Failure

→ reject request

## Rate Limit Exceeded

→ throttled response

---

# 17. Anti-Patterns

- direct service exposure bypassing gateway
- inconsistent API versions
- mixing AI and non-AI endpoints without routing
- missing tenant context
- unversioned public endpoints

---

# 18. Scalability Model

API Gateway must support:

- horizontal scaling
- stateless operation
- distributed rate limiting
- multi-region deployment (future)

---

# 19. Future Extensions

- GraphQL layer (optional abstraction)
- MCP-native gateway routing
- AI-driven request optimization
- predictive prefetch routing
- unified multi-channel API layer

---

# 20. Summary

The API Gateway is the **controlled entry point of ATLAS external systems**.

It ensures:

- security
- observability
- routing correctness
- AI integration consistency

It is the enforcement layer between external world and internal architecture.
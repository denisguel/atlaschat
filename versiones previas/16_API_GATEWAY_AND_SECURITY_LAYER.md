# 16_API_GATEWAY_AND_SECURITY_LAYER.md

---
id: ARCH-0016
title: API Gateway and Security Layer
status: Accepted
version: 1.0.0
date: 2026-06-29
owner: Platform Security
type: Architecture Specification
classification: Critical
related:
  - RFC-0010
  - ARCH-0012
  - ARCH-0011
---

# 1. Purpose

This document defines the **API Gateway and Security Layer** of ATLAS.

It is the first execution boundary for all external and internal requests, enforcing authentication, authorization, rate limits, validation, and tenant isolation.

No request reaches the system without passing this layer.

---

# 2. Design Philosophy

The API Gateway is designed as a **zero-trust enforcement layer**.

Principles:

- trust nothing by default
- validate everything at entry
- enforce tenant isolation early
- block unsafe requests before execution
- log all access attempts
- minimize downstream security burden

---

# 3. High-Level Architecture

```
Client / AI / Service
        │
        ▼
   API Gateway
        │
 ┌──────┼───────────────┐
 ▼      ▼               ▼
Auth  Validation   Rate Limiter
        │
        ▼
Request Router
        │
        ▼
Orchestrator / Services
```

---

# 4. Responsibilities

The API Gateway handles:

- authentication
- authorization
- request validation
- schema enforcement
- rate limiting
- tenant resolution
- request routing
- security logging

It does NOT handle business logic.

---

# 5. Authentication Model

Supported methods:

- JWT tokens
- API keys (service-to-service)
- OAuth (future integration)
- session-based auth (web)

Every request must include identity context.

---

# 6. Authorization Model

Authorization is enforced via:

- role-based access control (RBAC)
- tenant-based access control
- tool-level permissions
- domain-level restrictions

Example roles:

- Owner
- Operator
- Staff
- System Agent
- External Integration

---

# 7. Tenant Resolution

Every request must resolve:

```
organization_id
```

Resolution sources:

- JWT claims
- API key metadata
- session context
- header fallback (internal only)

If tenant cannot be resolved → request is rejected.

---

# 8. Request Validation

All requests must pass:

- JSON schema validation
- payload size limits
- type checking
- required field verification

Invalid requests are rejected immediately.

---

# 9. Rate Limiting

Rate limits are applied per:

- organization
- user
- API key
- IP (secondary layer)

Policies include:

- burst limits
- sustained limits
- endpoint-specific limits

AI endpoints may have separate quotas.

---

# 10. Request Routing

The Gateway routes requests to:

- AI Orchestrator
- Domain Services
- Workflow Engine
- Tool Execution Layer
- Event ingestion endpoints

Routing is deterministic and versioned.

---

# 11. Security Logging

Every request logs:

- request_id
- identity
- tenant_id
- endpoint
- payload hash
- response status
- latency
- rate limit state

Logs are immutable and audit-ready.

---

# 12. Threat Protection

The Gateway mitigates:

- injection attacks
- malformed payloads
- abuse patterns
- unauthorized access
- cross-tenant leakage attempts
- replay attacks

---

# 13. AI-Specific Security Rules

AI-originated requests are restricted:

- must use tool layer (no direct DB access)
- cannot bypass validation
- must respect tenant isolation
- must pass execution guard layer

AI is treated as an untrusted client.

---

# 14. Tool Execution Protection

Before tool execution:

Gateway ensures:

1. tool exists and is registered
2. version is valid
3. permissions are granted
4. input schema is valid
5. tenant scope is correct

---

# 15. Error Handling

Standard responses:

- 400 → invalid request
- 401 → unauthenticated
- 403 → unauthorized
- 429 → rate limited
- 500 → internal failure

Errors are structured and machine-readable.

---

# 16. Observability

Gateway metrics include:

- request volume
- error rate
- latency percentiles
- blocked requests
- auth failures
- rate limit triggers

---

# 17. Performance Requirements

Targets:

- authentication check: < 10ms
- validation: < 20ms
- routing: < 5ms
- total gateway overhead: < 50ms

---

# 18. Scalability

Gateway must support:

- horizontal scaling
- stateless operation
- distributed rate limiting
- edge deployment (future CDN integration)

---

# 19. Multi-Tenant Isolation

Hard guarantees:

- no cross-tenant request leakage
- strict tenant context binding
- enforced query scoping downstream

---

# 20. Anti-Patterns

Forbidden:

- business logic in gateway
- inconsistent auth enforcement
- bypassable validation paths
- shared tenant contexts
- hidden routing rules
- unlogged security decisions

---

# 21. Future Evolution

The Gateway is designed to evolve toward:

- edge-native security gateways
- AI-assisted threat detection
- behavioral anomaly detection
- distributed global routing
- MCP-compatible request layer
- adaptive rate limiting based on behavior

---

# 22. Success Criteria

The API Gateway is successful when:

- no unauthorized request reaches internal systems
- tenant isolation is never violated
- latency overhead remains minimal
- all requests are fully traceable
- security enforcement is consistent across all endpoints

---

# 23. Summary

The API Gateway and Security Layer is the enforcement boundary of ATLAS.

It ensures that all system interactions are validated, authorized, isolated and observable before reaching any internal service, enabling a secure and scalable foundation for AI-native hospitality infrastructure.
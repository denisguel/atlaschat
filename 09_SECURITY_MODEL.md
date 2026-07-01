# 09_SECURITY_MODEL.md

---
id: ARCH-009
title: Security Model
status: canonical
version: 1.0.0
type: SPEC
owner: Architecture
classification: Critical
---

# 1. Purpose

This document defines the security architecture of ATLAS.

Security is treated as a first-class architectural concern rather than an infrastructure concern.

Every component, service, workflow and AI agent must comply with this specification.

---

# 2. Security Principles

ATLAS adopts the following principles:

- Security by Design
- Least Privilege
- Zero Trust
- Defense in Depth
- Explicit Authorization
- Tenant Isolation
- Auditability
- Secure Defaults

---

# 3. Security Architecture

```
User
↓

Authentication

↓

Authorization

↓

API Gateway

↓

AI Orchestrator

↓

Domain Services

↓

Database

↓

Audit Log
```

No component may bypass another security layer.

---

# 4. Authentication

Authentication is delegated to Supabase Auth.

Supported methods:

- Email + Password
- Magic Link
- OAuth Providers
- Future Enterprise SSO

Authentication proves identity only.

It never grants permissions.

---

# 5. Authorization

Authorization is role-based and tenant-aware.

Initial roles:

- Platform Admin
- Organization Admin
- Property Manager
- Staff
- Read Only
- Guest (future)
- System Agent

Every request requires authorization validation.

---

# 6. Tenant Isolation

ATLAS is multi-tenant.

Tenant isolation is mandatory.

Isolation levels:

- Authentication
- Authorization
- Database
- Storage
- Events
- Memory
- Analytics
- AI Context

Cross-tenant access is forbidden unless explicitly implemented as a platform feature.

---

# 7. Row Level Security

All business tables must enforce Row Level Security.

Policies must guarantee:

- tenant isolation
- ownership validation
- role validation

Application code must never replace database security.

---

# 8. Secrets Management

Secrets must never be stored:

- in frontend code
- in repositories
- inside prompts
- inside workflows

Secrets are managed through secure environment variables.

---

# 9. AI Security

AI introduces additional attack surfaces.

All AI requests must implement:

- prompt isolation
- context filtering
- tool permission validation
- output validation
- hallucination mitigation
- maximum context boundaries

---

# 10. Prompt Injection Protection

The Orchestrator must detect attempts to:

- override system instructions
- expose internal prompts
- execute unauthorized tools
- reveal secrets
- manipulate business rules

Potential attacks must terminate execution immediately.

---

# 11. Tool Permission Model

Every AI tool declares:

- allowed roles
- allowed tenants
- required permissions
- allowed parameters

The AI cannot invoke arbitrary tools.

All executions are validated.

---

# 12. Data Classification

ATLAS classifies data into four levels.

## Public

Marketing content.

Examples:

- property descriptions
- amenities
- photos

---

## Internal

Operational information.

Examples:

- workflows
- occupancy metrics

---

## Confidential

Business data.

Examples:

- reservations
- pricing
- revenue
- contracts

---

## Restricted

Highly sensitive data.

Examples:

- authentication
- tokens
- payment references
- audit records

---

# 13. Encryption

Encryption in transit:

- TLS required

Encryption at rest:

- database
- storage
- backups

Sensitive fields may require application-level encryption.

---

# 14. Audit Logging

Every critical action must generate:

- timestamp
- actor
- tenant
- operation
- affected resource
- correlation ID

Audit records are immutable.

---

# 15. AI Audit Trail

Every AI decision must record:

- model
- prompt version
- retrieved context
- selected tools
- execution plan
- generated response

This enables complete explainability.

---

# 16. API Security

API Gateway enforces:

- JWT validation
- rate limiting
- request validation
- payload sanitization
- CORS policies

No service is publicly exposed.

---

# 17. File Security

Storage rules:

- tenant isolation
- signed URLs
- expiration
- MIME validation
- antivirus scanning (future)

Supported files:

- images
- PDFs
- documents

Executable files are rejected.

---

# 18. Event Security

Events inherit tenant permissions.

Requirements:

- immutable
- signed internally
- traceable
- replay protected

Unauthorized subscribers cannot consume events.

---

# 19. Memory Security

Brains contain enriched information.

Context retrieval must enforce:

- tenant boundaries
- role filtering
- context minimization
- purpose limitation

AI receives only the minimum required context.

---

# 20. Availability Protection

Protection mechanisms:

- rate limiting
- retry policies
- circuit breakers
- workload isolation
- queue backpressure

The system must remain operational under partial failures.

---

# 21. Incident Response

Security incidents require:

- automatic detection
- alert generation
- audit preservation
- forensic traceability

No incident may silently disappear.

---

# 22. Compliance Targets

Architecture should support future compliance with:

- GDPR
- LGPD
- SOC 2
- ISO 27001

Compliance implementation is documented separately.

---

# 23. Anti-Patterns

Forbidden practices:

- secrets inside prompts
- frontend authorization
- disabled RLS
- shared service accounts
- unrestricted AI tools
- tenant filtering in frontend only
- direct database exposure
- mutable audit logs

---

# 24. Future Evolution

Future versions will include:

- Attribute-Based Access Control (ABAC)
- Fine-grained AI permissions
- Hardware security modules
- End-to-end encrypted guest conversations
- Confidential computing support
- AI security policy engine

---

# 25. Summary

Security in ATLAS is enforced across every architectural layer.

Authentication verifies identity.

Authorization verifies permissions.

Row Level Security protects data.

AI operates under explicit permission boundaries.

Every critical action is traceable, auditable and reproducible.

Security is a property of the architecture, not a feature of the implementation.
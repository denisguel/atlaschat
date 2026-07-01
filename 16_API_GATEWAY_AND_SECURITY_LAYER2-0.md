# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : DOC-0016
# DOCUMENT    : API Gateway and Security Layer
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Core Infrastructure
#
# REPLACES
#
# 16_API_GATEWAY_AND_SECURITY_LAYER.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0002 AI Orchestrator
# RFC-0010 Tool Invocation & Execution Layer
#
# ARCH-0022 Business Decision Validation Layer
# ARCH-0025 MVP Physical Architecture
#
# ============================================================================

# API GATEWAY AND SECURITY LAYER

---

# 1. Purpose

This document defines the external entry point of ATLAS.

The API Gateway is responsible for protecting the platform, authenticating requests, enforcing authorization rules and routing validated traffic toward the application layer.

Business logic never executes inside the gateway.

---

# 2. Architectural Principles

The Gateway follows five immutable principles.

## Principle 1

Every request is authenticated.

---

## Principle 2

Every request is authorized.

---

## Principle 3

Every request is observable.

---

## Principle 4

Business logic belongs to Application Services.

---

## Principle 5

The Gateway remains stateless.

---

# 3. Responsibilities

The API Gateway is responsible for:

Authentication

Authorization

Routing

Rate Limiting

Request Validation

API Versioning

Observability

Security Enforcement

It is not responsible for business decisions.

---

# 4. Request Lifecycle

```
Client

↓

API Gateway

↓

Authentication

↓

Authorization

↓

Validation

↓

Application Service

↓

Business Validation

↓

Database

↓

Response
```

Every request follows the same deterministic pipeline.

---

# 5. Authentication

Authentication is delegated to Supabase Auth.

Supported methods include:

Email & Password

Magic Links

OAuth Providers

Future Enterprise SSO

JWT Tokens

Authentication occurs before any business processing.

---

# 6. Authorization

Authorization follows Role-Based Access Control (RBAC).

Typical roles include:

Platform Administrator

Organization Owner

Property Manager

Staff

Guest

Service Account

Permissions are evaluated for every request.

---

# 7. Tenant Isolation

Every authenticated request belongs to exactly one organization.

The Gateway propagates:

Organization ID

User ID

Role

Permission Context

Tenant boundaries remain enforced throughout the request lifecycle.

---

# 8. Request Validation

Incoming requests are validated before reaching business services.

Validation includes:

Schema Validation

Required Fields

Data Types

Payload Size

Content-Type

Malformed requests are rejected immediately.

---

# 9. API Versioning

Public APIs are versioned.

Example:

/api/v1/

↓

/api/v2/

Breaking changes never modify existing public contracts.

---

# 10. Rate Limiting

Rate limiting protects platform stability.

Examples:

Anonymous Requests

↓

Strict Limits

--------------------------------

Authenticated Users

↓

Higher Limits

--------------------------------

Internal Services

↓

Configurable Limits

Rate limiting policies are configurable per endpoint.

---
# 11. Transport Security

All communications SHALL use HTTPS.

Requirements include:

TLS 1.2+

↓

Secure Certificates

↓

HSTS

↓

Forward Secrecy

↓

Automatic Certificate Renewal

Plain HTTP is permanently redirected or rejected.

---

# 12. JWT Validation

Every authenticated request validates:

Token Signature

↓

Expiration

↓

Issuer

↓

Audience

↓

Organization

↓

Permissions

Expired or invalid tokens are rejected before routing.

---

# 13. API Keys

Machine-to-machine integrations use API Keys.

Each key contains:

Organization Scope

↓

Permissions

↓

Creation Date

↓

Expiration Date (optional)

↓

Last Usage

↓

Status

API Keys never replace user authentication.

---

# 14. Service Accounts

Automated integrations authenticate through Service Accounts.

Examples:

N8N

Background Jobs

Future MCP Servers

Webhook Consumers

Each Service Account follows the Principle of Least Privilege.

---

# 15. Webhook Security

Incoming webhooks require:

Signature Validation

↓

Timestamp Validation

↓

Replay Protection

↓

Payload Validation

↓

Authentication

Unauthenticated webhooks are rejected.

---

# 16. CORS Policy

Cross-Origin Resource Sharing is explicitly configured.

Allowed origins include:

Official Dashboard

Official Booking Engine

Approved Domains

Development Environments

Wildcard origins are prohibited in production.

---

# 17. Input Sanitization

All external input is sanitized.

Protection includes:

SQL Injection

Cross-Site Scripting (XSS)

Header Injection

Command Injection

Path Traversal

Sanitization complements parameterized queries and schema validation.

---

# 18. Business Validation Boundary

Security validation ends before business execution.

Pipeline:

Gateway Validation

↓

Authentication

↓

Authorization

↓

Application Service

↓

Business Decision Validation

↓

Database Transaction

The Gateway never evaluates business rules.

---

# 19. Observability

Every request generates telemetry.

Captured information includes:

Request ID

Correlation ID

User ID

Organization ID

Endpoint

HTTP Method

Latency

Response Code

Authentication Result

Authorization Result

Telemetry supports monitoring, debugging and auditing.

---

# 20. Error Handling

Errors follow a standardized structure.

Categories include:

Authentication Errors

Authorization Errors

Validation Errors

Business Errors

Infrastructure Errors

Internal Errors

Sensitive implementation details are never exposed to clients.

---
# 21. Audit Logging

Security-sensitive operations generate immutable audit records.

Examples:

User Login

↓

Permission Change

↓

API Key Creation

↓

Configuration Update

↓

Authentication Failure

↓

Administrative Action

Audit records include:

- Timestamp
- User ID
- Organization ID
- Action
- Resource
- Result
- Correlation ID
- Source IP (when applicable)

Audit logs are append-only.

---

# 22. Secret Management

Sensitive credentials SHALL never be stored in source code.

Secrets include:

JWT Signing Keys

API Keys

OAuth Secrets

Database Credentials

Encryption Keys

Third-Party Tokens

Secrets are managed through secure environment configuration and rotation policies.

---

# 23. API Documentation

Every public endpoint shall be documented.

Documentation includes:

Endpoint

HTTP Method

Authentication Requirements

Request Schema

Response Schema

Error Codes

Rate Limits

Examples

API documentation is versioned together with the API.

---

# 24. Backward Compatibility

Public APIs preserve compatibility whenever possible.

Rules:

Non-breaking additions are allowed.

↓

Breaking changes require a new API version.

↓

Deprecated endpoints remain available during the migration period.

This minimizes disruption for integrations.

---

# 25. Internal APIs

Internal services communicate through private APIs.

Characteristics:

Authenticated

Authorized

Versioned

Observable

Not publicly exposed

Internal APIs may evolve faster than public APIs while preserving service contracts.

---

# 26. Security Monitoring

Security telemetry continuously monitors:

Authentication Failures

Authorization Failures

Rate Limit Violations

Suspicious Traffic

Token Abuse

API Key Abuse

Repeated Validation Errors

Security anomalies generate alerts for operational review.

---

# 27. Anti-Patterns

Forbidden:

Business Logic inside the Gateway.

Direct database access from the Gateway.

Long-running processing.

Trusting client-side validation.

Hardcoded secrets.

Anonymous administrative endpoints.

Skipping authorization after authentication.

Returning sensitive stack traces.

---

# 28. Future Evolution

Future capabilities may include:

API Gateway Clustering

Web Application Firewall (WAF)

Adaptive Rate Limiting

Bot Detection

Geo-based Access Policies

Mutual TLS

Zero Trust Networking

Enterprise SSO

These enhancements shall preserve the existing security contracts.

---

# 29. Relationship with AI Components

The Gateway is AI-agnostic.

AI requests follow the same security pipeline as every other request.

Neither the AI Orchestrator nor specialized agents bypass authentication, authorization or validation.

Security precedes intelligence.

---

# 30. Final Statement

The API Gateway and Security Layer provides the secure entry point for every interaction with ATLAS.

By separating authentication, authorization and transport security from business execution, the platform maintains a clear security boundary while enabling deterministic services, AI orchestration and future scalability without compromising tenant isolation, observability or operational integrity.
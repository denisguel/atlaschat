# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : DOC-0017
# DOCUMENT    : Deployment and Infrastructure
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Infrastructure
#
# REPLACES
#
# 17_DEPLOYMENT_AND_INFRASTRUCTURE.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# RFC-0002 AI Orchestrator
#
# ARCH-0025 MVP Physical Architecture
# ARCH-0026 Workflow Responsibility Model
# ARCH-0027 AI Cost Optimization Strategy
# ARCH-0028 AI Model Routing
#
# ============================================================================

# DEPLOYMENT AND INFRASTRUCTURE

---

# 1. Purpose

This document defines the physical deployment architecture of ATLAS.

The infrastructure is designed to maximize simplicity during the MVP while preserving a clear migration path toward enterprise-scale deployment.

Infrastructure decisions prioritize:

- operational simplicity;
- reliability;
- cost efficiency;
- observability;
- scalability.

---

# 2. Architectural Principles

## Principle 1

Deploy a modular monolith for the MVP.

---

## Principle 2

Scale only when measurable bottlenecks appear.

---

## Principle 3

Infrastructure complexity must follow business growth.

---

## Principle 4

Every component must be independently replaceable.

---

## Principle 5

Cloud-native design without premature microservices.

---

# 3. MVP Infrastructure

The MVP consists of:

Next.js Application

↓

Supabase

↓

N8N

↓

LLM Providers

↓

External APIs

↓

Storage

All components remain logically separated while running as a single deployable platform.

---

# 4. Primary Components

Frontend

Next.js

Backend

Next.js Server

Database

Supabase PostgreSQL

Authentication

Supabase Auth

Storage

Supabase Storage

Automation

N8N

AI

OpenAI / Google / Anthropic

Monitoring

Application Logs + Metrics

---

# 5. Deployment Model

```
Internet

↓

Cloudflare (future)

↓

Next.js Application

↓

Application Services

↓

Supabase

↓

Storage

↓

N8N

↓

External Providers
```

Infrastructure remains intentionally simple during the MVP.

---

# 6. Application Deployment

The application is deployed as a single service.

Benefits:

Single repository

Single deployment pipeline

Lower operational complexity

Simpler debugging

Lower infrastructure costs

Future decomposition remains possible.

---

# 7. Stateless Services

Application instances remain stateless.

Persistent state exists only in:

PostgreSQL

Storage

Vector Database

No business session is stored in server memory.

---

# 8. Environment Configuration

Configuration is externalized.

Examples:

Database URL

API Keys

JWT Secrets

Webhook Secrets

Feature Flags

Environment-specific values never reside in source code.

---

# 9. Infrastructure Environments

Minimum environments:

Development

↓

Staging

↓

Production

Each environment remains isolated.

Production data is never used during development.

---

# 10. CI/CD

Deployment pipeline:

Git Commit

↓

Pull Request

↓

Review

↓

Tests

↓

Build

↓

Deployment

↓

Health Check

↓

Monitoring

Deployment is automated whenever possible.

---
# 11. Scalability Strategy

Infrastructure scales incrementally.

Scaling priorities:

Application Instances

↓

Database Resources

↓

Read Replicas

↓

Background Workers

↓

Caching

↓

Dedicated AI Services (future)

Infrastructure growth follows measured demand rather than anticipated demand.

---

# 12. AI Infrastructure

AI providers are abstracted behind the AI Orchestrator.

Supported providers may include:

OpenAI

Google Gemini

Anthropic Claude

Local Models (future)

Provider selection follows the routing strategy defined in ARCH-0028.

The application layer never depends directly on a specific LLM vendor.

---

# 13. Workflow Infrastructure

Workflow execution is delegated to N8N during the MVP.

Responsibilities include:

Guest Notifications

↓

Scheduled Messages

↓

Reminder Workflows

↓

OTA Synchronization

↓

Background Automation

Business rules remain inside Application Services.

N8N orchestrates side effects rather than business decisions.

---

# 14. Storage Strategy

Persistent files are stored separately from operational data.

Examples:

Property Images

Documents

Invoices

Contracts

Guest Attachments

Generated Reports

Metadata remains inside PostgreSQL.

Binary assets reside in Object Storage.

---

# 15. Caching Strategy

Caching is introduced only after measurable bottlenecks.

Potential cache targets:

Public Property Data

Configuration

Static Content

Reference Data

Computed Dashboards

Operational business transactions bypass cache to preserve consistency.

---

# 16. Background Processing

Long-running operations execute asynchronously.

Examples:

Embedding Generation

AI Summarization

Large Imports

Bulk Notifications

Analytics Refresh

Background execution never blocks user interactions.

---

# 17. Observability

Infrastructure monitoring includes:

Application Health

↓

CPU Usage

↓

Memory Usage

↓

Database Connections

↓

API Latency

↓

Workflow Execution

↓

AI Provider Latency

↓

Storage Consumption

All metrics feed centralized monitoring.

---

# 18. Logging

Logs follow structured logging principles.

Every log includes:

Timestamp

Correlation ID

Organization ID

Service

Severity

Execution ID

Message

Logs are machine-readable and searchable.

---

# 19. Health Checks

Every deployable component exposes health endpoints.

Health validation includes:

Application

Database

Storage

Authentication

AI Providers

Workflow Engine

Health endpoints distinguish:

Healthy

↓

Degraded

↓

Unavailable

---

# 20. Failure Strategy

Infrastructure failures follow graceful degradation.

Examples:

LLM unavailable

↓

Fallback Model

--------------------------------

Workflow Engine unavailable

↓

Retry Queue

--------------------------------

Storage unavailable

↓

Temporary Failure Response

Critical failures never corrupt business state.

---
# 21. Backup Strategy

ATLAS implements a layered backup strategy.

Operational Database

↓

Point-in-Time Recovery

↓

Daily Snapshots

↓

Weekly Full Backup

↓

Offsite Backup

Backup procedures are periodically validated through restoration testing.

Backups are considered valid only after successful recovery verification.

---

# 22. Disaster Recovery

The platform defines the following recovery objectives.

Recovery Point Objective (RPO)

≤ 5 minutes

Recovery Time Objective (RTO)

≤ 30 minutes

Disaster Recovery procedures are documented, version controlled and tested regularly.

Operational continuity always takes precedence over infrastructure convenience.

---

# 23. Security Infrastructure

Infrastructure security includes:

TLS Encryption

↓

Secret Management

↓

Network Isolation

↓

Role-Based Access

↓

Database Encryption

↓

Storage Encryption

↓

Audit Logging

Infrastructure security follows the Principle of Least Privilege.

---

# 24. Cost Optimization

Infrastructure cost is continuously monitored.

Optimization priorities include:

Right-sized Compute

↓

AI Model Routing

↓

Workflow Optimization

↓

Storage Lifecycle Policies

↓

Database Optimization

↓

Observability-driven Scaling

Infrastructure expenses must remain proportional to business growth.

---

# 25. Infrastructure Observability

Every infrastructure component exports telemetry.

Metrics include:

CPU

Memory

Disk Usage

Database Performance

Network Latency

Workflow Queue Length

AI Provider Latency

Deployment Frequency

Error Rate

These metrics support proactive operations.

---

# 26. Release Strategy

Deployments follow progressive release principles.

Development

↓

Staging

↓

Production

↓

Monitoring

↓

Rollback (if necessary)

Deployment automation minimizes operational risk.

---

# 27. Rollback Strategy

Every deployment must support rollback.

Rollback includes:

Application Version

↓

Database Migration Validation

↓

Configuration Verification

↓

Health Checks

↓

Traffic Restoration

Rollback procedures are tested before production use.

---

# 28. Infrastructure Anti-Patterns

The following practices are prohibited:

Premature Microservices.

Shared Production Credentials.

Manual Production Changes.

Hardcoded Secrets.

Infrastructure Drift.

Vendor Lock-in without Abstraction.

Scaling Without Metrics.

Embedding Business Logic into Infrastructure Components.

Infrastructure must remain deterministic, reproducible and observable.

---

# 29. Future Evolution

Future infrastructure improvements may include:

Kubernetes Deployment

Horizontal Auto Scaling

Dedicated Worker Pools

Redis Caching

CDN Integration

Dedicated Vector Database

Multi-Region Deployment

Service Mesh

Dedicated Event Bus

Infrastructure evolution must remain incremental and business-driven.

---

# 30. Final Statement

The Deployment and Infrastructure Architecture provides the operational foundation for ATLAS.

By adopting a cloud-native modular monolith, deterministic deployment pipeline and infrastructure-first observability, ATLAS achieves low operational complexity during the MVP while preserving a clear migration path toward enterprise-scale deployment.

Infrastructure decisions prioritize reliability, maintainability, cost efficiency and measurable scalability rather than premature architectural complexity.

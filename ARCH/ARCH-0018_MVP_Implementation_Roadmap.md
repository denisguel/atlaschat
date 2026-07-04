# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0018
# DOCUMENT    : MVP Implementation Roadmap
# VERSION     : 2.0
# STATUS      : ACCEPTED
# CLASS       : Product Roadmap
#
# REPLACES
#
# 18_MVP_IMPLEMENTATION_ROADMAP.md (v1)
#
# ============================================================================
#
# REFERENCES
#
# All RFC Documents
#
# ARCH-0025 MVP Physical Architecture
# ARCH-0026 Workflow Responsibility Model
# ARCH-0027 AI Cost Optimization Strategy
#
# ============================================================================

# MVP IMPLEMENTATION ROADMAP

---

# 1. Purpose

This document defines the implementation roadmap for the ATLAS MVP.

Its objective is to deliver the smallest product capable of generating measurable business value while preserving the long-term architecture defined throughout this documentation.

The roadmap prioritizes:

- speed of delivery;
- validation with real customers;
- low operational cost;
- architectural consistency.

---

# 2. MVP Philosophy

The MVP is not a reduced version of the final product.

It is the first production-ready implementation of the ATLAS architecture.

Every implemented feature must satisfy at least one measurable business objective.

---

# 3. Guiding Principles

Implementation follows five principles.

## Principle 1

Business value before technical elegance.

---

## Principle 2

One deployable application.

---

## Principle 3

Reuse existing infrastructure whenever possible.

---

## Principle 4

Avoid premature optimization.

---

## Principle 5

Every iteration must remain deployable.

---

# 4. MVP Technology Stack

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

Workflow Engine

N8N

AI Providers

OpenAI

Google Gemini

Anthropic (optional)

Deployment

Vercel

---

# 5. Phase 0 — Foundation

Deliverables:

Repository

CI/CD

Supabase Project

Authentication

Environment Configuration

Database Schema

Basic Monitoring

This phase establishes the technical foundation.

---

# 6. Phase 1 — Core Platform

Deliverables:

Organizations

Properties

Units

Reservations

Guests

Users

Permissions

Dashboard

This phase enables basic PMS functionality.

---

# 7. Phase 2 — AI Foundation

Deliverables:

AI Orchestrator

Context Builder

Execution Levels

Prompt Framework

Memory Retrieval

Tool Invocation Layer

Business Validation

This phase enables deterministic AI execution.

---

# 8. Phase 3 — Guest Operations

Deliverables:

WhatsApp Integration

Guest Agent

Reservation Agent

Conversation History

FAQ

Booking Assistance

Operational Messaging

The goal is reducing manual guest communication.

---

# 9. Phase 4 — Revenue Intelligence

Deliverables:

Revenue Brain

Pricing Recommendations

Demand Forecasting

Occupancy Forecasting

Gap Night Detection

Revenue Dashboard

Revenue KPIs

Recommendations remain explainable and optionally require owner approval.

---

# 10. Phase 5 — Growth Intelligence

Deliverables:

Growth Brain

Attribution Engine

Lead Intelligence

Content Intelligence

WhatsApp Conversion Metrics

Growth Dashboard

Commercial Recommendations

The objective is increasing direct bookings.

---
# 11. Phase 6 — Workflow Automation

Deliverables:

N8N Integration

Cleaning Workflows

Check-in Automation

Check-out Automation

Reminder Automation

Payment Reminders

Owner Notifications

Workflow execution follows the Workflow Responsibility Model.

Business rules remain inside the application.

---

# 12. Phase 7 — Booking Engine

Deliverables:

Direct Booking Engine

Availability Search

Booking Flow

Reservation Confirmation

Payment Integration

Owner Configuration

SEO-friendly Property Pages

The Booking Engine becomes the primary direct sales channel.

---

# 13. Phase 8 — Operational Intelligence

Deliverables:

Operations Agent

Task Recommendations

Maintenance Tracking

Cleaning Coordination

Operational Dashboard

Knowledge Base

Operational intelligence assists staff without replacing deterministic workflows.

---

# 14. Phase 9 — Analytics

Deliverables:

Revenue Dashboard

Growth Dashboard

Operational Dashboard

Executive Dashboard

Forecast Reports

Business KPIs

AI Performance Metrics

Analytics provide actionable insights rather than raw data.

---

# 15. Phase 10 — Production Hardening

Deliverables:

Performance Optimization

Security Review

Load Testing

Backup Validation

Disaster Recovery Testing

Observability

Cost Optimization

Production readiness is validated before public rollout.

---

# 16. MVP Success Criteria

The MVP is considered successful when it can:

Manage real properties.

Receive WhatsApp inquiries.

Create and modify reservations.

Generate AI-assisted responses.

Recommend pricing.

Recommend marketing actions.

Execute operational workflows.

Support paying customers.

Architecture completeness is secondary to business validation.

---

# 17. Out of Scope

The following capabilities are intentionally excluded from the MVP:

Portfolio Revenue Optimization

Multi-region Deployment

Voice AI

Vision AI

Autonomous Campaign Generation

Marketplace

Advanced Yield Management

Dynamic Agent Creation

Enterprise Multi-brand Support

These capabilities remain part of the long-term roadmap.

---

# 18. Validation Strategy

Every phase concludes with measurable validation.

Validation includes:

Functional Testing

↓

Integration Testing

↓

Performance Testing

↓

User Acceptance Testing

↓

Production Monitoring

Each phase must produce working software before continuing.

---

# 19. Risk Management

Primary implementation risks include:

AI Costs

↓

Scope Creep

↓

Workflow Complexity

↓

External API Changes

↓

Operational Adoption

↓

Performance Bottlenecks

Each identified risk requires mitigation before entering production.

---

# 20. AI Rollout Strategy

AI capabilities are introduced incrementally.

Order of adoption:

Retrieval

↓

Recommendations

↓

Decision Support

↓

Semi-Automation

↓

Controlled Automation

↓

Policy-Driven Automation

Fully autonomous execution is never the initial deployment model.

---
# 21. Cost Strategy

The MVP shall maintain operational costs proportional to customer growth.

Cost priorities:

Low Infrastructure Cost

↓

Low AI Token Consumption

↓

Minimal Operational Overhead

↓

Incremental Scaling

↓

Continuous Cost Monitoring

Every architectural decision shall consider its impact on Gross Incremental Profit (IGPPP).

---

# 22. Success Metrics

The MVP continuously measures business outcomes.

Core metrics include:

Active Organizations

Active Properties

Monthly Reservations

Direct Booking Ratio

Revenue Managed

AI Response Latency

Workflow Success Rate

Owner Satisfaction

Guest Satisfaction

Infrastructure Cost per Property

Success is measured by customer value rather than feature count.

---

# 23. Deployment Strategy

The rollout follows controlled expansion.

Internal Testing

↓

Pilot Customers

↓

Early Adopters

↓

Limited Public Release

↓

General Availability

Each stage requires measurable stability before progressing.

---

# 24. Documentation Strategy

Every implemented feature must include:

Architecture Documentation

Technical Documentation

API Documentation

Operational Documentation

User Documentation

Documentation evolves together with the codebase.

---

# 25. Testing Strategy

Testing layers include:

Unit Tests

↓

Integration Tests

↓

Workflow Tests

↓

AI Validation Tests

↓

End-to-End Tests

↓

Manual Acceptance Tests

Testing focuses on business reliability rather than code coverage alone.

---

# 26. Monitoring Strategy

Production monitoring includes:

Application Health

Database Performance

Workflow Execution

AI Usage

API Latency

Business KPIs

Security Events

Monitoring enables proactive operations instead of reactive troubleshooting.

---

# 27. Change Management

Every architectural change follows a controlled process.

Proposal

↓

Technical Review

↓

Implementation

↓

Validation

↓

Deployment

↓

Post-Deployment Review

Architecture evolves through documented decisions rather than ad hoc modifications.

---

# 28. Roadmap Governance

Roadmap priorities are determined by business value.

Priority order:

Customer Value

↓

Revenue Impact

↓

Operational Simplicity

↓

Technical Debt Reduction

↓

Future Scalability

Technical sophistication never outweighs validated customer needs.

---

# 29. Long-Term Evolution

Following MVP validation, future phases may include:

Enterprise Multi-Property Management

Advanced Revenue Optimization

Marketplace Integrations

Multi-Language Expansion

Multi-Country Compliance

Voice Interfaces

Vision Capabilities

Advanced AI Agents

Distributed Infrastructure

These initiatives begin only after achieving sustainable product-market fit.

---

# 30. Final Statement

The MVP Implementation Roadmap defines a disciplined path from concept to production.

By prioritizing measurable customer value, deterministic architecture, incremental AI adoption and operational simplicity, ATLAS minimizes execution risk while preserving a scalable foundation for future enterprise capabilities.

The roadmap is intentionally business-driven: every completed phase must deliver observable value before additional complexity is introduced.
# ATLAS — Índice Maestro del Proyecto (v3.0 — Documentación Cerrada Pre-Código)

**Estado:** Canonical
**Fecha:** 2026-07-03

---

## Propósito

Corpus documental cerrado para iniciar generación de código. 55 documentos canónicos, cero gaps críticos de arquitectura/seguridad sin resolver o explícitamente diferidos con justificación.

---

## 1. FOUNDATION (5)

AES-00 Project DNA · ATLAS Spec Repo · ATLAS Master Specification · 01 Product Principles · 02 MVP Scope

## 2. ADR — Decisiones de Arquitectura (12)

| ID | Título | Estado |
|---|---|---|
| ADR-001 | Supabase as Foundation | Accepted |
| ADR-002 | Event-Driven Architecture | Accepted |
| ADR-003 | WhatsApp as Primary Interface | Accepted — ver enmienda |
| **ADR-003-A** | **Telegram Interim Channel Amendment** | **Accepted — nuevo** |
| ADR-004 | Multi-Agent Architecture | Accepted |
| ADR-005 | Brain Memory Architecture | Accepted |
| ADR-006 | n8n as Workflow Engine | Accepted |
| ADR-007 | MCP-Ready Architecture | Accepted |
| ADR-008 | AI Provider Abstraction | Accepted — ver ADR-011 |
| ADR-009 | Event Sourcing Ready | Accepted |
| **ADR-010** | **Phase 1 Scope Reduction** | **Accepted — nuevo, cierra gap crítico** |
| **ADR-011** | **Claude as Primary AI Provider** | **Accepted — nuevo** |
| **ADR-011-A** | **Free Provider Testing Override (config-gated)** | **Accepted — nuevo. Override temporal a modelo gratuito para L3 en testing vía `REVENUE_AGENT_PROVIDER`; ADR-011 sigue vigente en prod** |

## 3. RFC — Propuestas Funcionales (10)

RFC-0001 a RFC-0010, sin cambios respecto a la revisión anterior.

## 4. ARCH — Catálogo de Arquitectura Técnica (28)

| ID | Título | Nota |
|---|---|---|
| ARCH-000 | Architecture Overview | |
| ARCH-002 | High Level Architecture | |
| **ARCH-003** | **System Components and Runtime (v2.0)** | **Promovido desde borrador — incorpora Execution Levels, BDVL, Cost Control, Context Builder a nivel de síntesis** |
| ARCH-004 a ARCH-010 | Domain Model, Services, Event Bus, Data Flow, API Gateway, Security Model, Scalability | Sin cambios |
| ARCH-0012 | AI Orchestration Implementation | |
| ARCH-0013 | Memory Architecture (v2.1) | **Incorpora Decision Graph y Política de Retención** |
| ARCH-0014 a ARCH-0018 | Multi-Agent, Event-Driven, API Gateway/Security, MVP Roadmap | IDs corregidos (colisión resuelta) |
| ARCH-0019 | Product Roadmap and Evolution | **Incorpora addendum estratégico (moats, success metrics, strategic constraints)** |
| **ARCH-0020** | **Master System Specification (v2.0)** | **Reescrita — sintetiza todo el corpus actualizado** |
| ARCH-0021 a ARCH-0027 | Execution Levels, BDVL, UX, State Consistency, MVP Physical, Workflow Responsibility, Cost Optimization | Sin cambios |
| **ARCH-0028** | **AI Safety and Guardrails** | **Escrito de cero — gap crítico cerrado** |
| ARCH-0029 | Context Retrieval Strategy | Sin cambios |
| ARCH-0030 | AI Model Routing and Provider Strategy | **Actualizado con tabla de routing de ADR-011** |

## 5. DOC — Namespace Secundario (3)

| ID | Título |
|---|---|
| DOC-0011 | Database Architecture |
| DOC-0017 | Deployment and Infrastructure Model |
| **DOC-0031** | **Security and Multi-Tenancy Handbook — nuevo, cierra gap de "apto para multiusuario"** |

## 6. Gaps restantes (priorizados — decisión editorial explícita, no omisión)

| Doc | Prioridad | Bloquea código de Phase 1? |
|---|---|---|
| Data Model Specification | 🔴 Alta | **Sí** — siguiente paso recomendado |
| Testing Strategy | 🔴 Alta | **Sí** — dado historial de bug de multi-tenancy |
| Prompt Engineering Standards | 🟡 Media | No bloquea inicio, sí antes de prompts de producción |
| API Specification (OpenAPI) | 🟡 Media | No — superficie de API de Phase 1 es pequeña |
| Coding Standards | 🟡 Media | No — recomendado, no bloqueante para un solo desarrollador |
| DOC-32 a DOC-60 restantes (Glossary, Onboarding, Playbook, DR Plan, Enterprise Scaling, etc.) | 🟢 Baja | No — se escriben iterativamente durante desarrollo, no antes |

**Recomendación editorial:** de los 27 documentos restantes del plan original (31-60), solo 2 (Data Model Spec, Testing Strategy) son verdaderamente bloqueantes para escribir código de Phase 1 sin riesgo. El resto es sobre-ingeniería documental en este punto — se generan cuando el código los necesite, no antes.

## 7. _ARCHIVE — Material preservado (19 archivos)

Todo lo superseded, sin excepción, preservado para trazabilidad. Ver `CHANGELOG_RECONCILIATION.md`.

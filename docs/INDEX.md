# ATLAS — Índice Maestro de Citación (F0, Entregable 3)

**Fecha:** 2026-07-21 · **Reemplaza como fuente de citación a:** `00_INDICE_MAESTRO.md` (desactualizado desde 2026-07-03 — no se borra, ver fila propia abajo).

Esta es la base de citación del Registry de F1. Metodología: `estado` y `gobierna` reflejan lo verificado en `F0_CORPUS_AUDIT.md` + esta sesión de trabajo sobre `atlas-mvp`; donde no hay evidencia directa (no leí el doc a fondo, o el código real no confirma/descarta su uso), la celda dice **A CONFIRMAR** en vez de asumir — no adivinar es preferible a completar la tabla.

**Leyenda `estado`:** VIGENTE (Accepted, sin objeción) · SUPERSEDIDO-por-X · REFERENCIA (visión/fase futura, no gobierna hoy) · A CONFIRMAR (necesita lectura o verificación de código que F0 no alcanzó).
**Leyenda `gobierna`:** sí / no / A CONFIRMAR — responde "¿una decisión de código de F1–F5 depende de este doc hoy?" (criterio de `F0_CORPUS_AUDIT.md` §2.3).

---

## FOUNDATION

| ID | Título | Tipo | Estado | Gobierna |
|---|---|---|---|---|
| AES-00 | Project DNA v0.1 | FOUNDATION | VIGENTE | sí |
| — | ATLAS Spec Repo | FOUNDATION | VIGENTE | sí |
| — | ATLAS Master Specification | FOUNDATION | VIGENTE | sí |
| 01 | Product Principles | FOUNDATION | VIGENTE | sí |
| 02 | MVP Scope | FOUNDATION | A CONFIRMAR — leer junto con ADR-010, puede haber quedado parcialmente superseded | sí (parcial) |

## ADR — Decisiones de Arquitectura

| ID | Título | Estado | Gobierna |
|---|---|---|---|
| ADR-001 | Supabase as Foundation | VIGENTE | sí |
| ADR-002 | Event-Driven Architecture | VIGENTE | sí |
| ADR-003 | WhatsApp-First Architecture | VIGENTE (tesis de mercado; timing enmendado por 003-A/003-B) | sí |
| ADR-003-A | Telegram Interim Channel Amendment | VIGENTE | sí |
| **ADR-003-B** | **Telegram como Puente hacia WhatsApp (Gate F5)** *(nuevo, F0)* | VIGENTE | sí |
| ADR-004 | Multi-Agent Architecture | VIGENTE (visión) — diferido a Fase 2+ por ADR-010 | **no** |
| ADR-005 | Brain Memory Architecture | VIGENTE (visión) — diferido a Fase 2+ por ADR-010 (ver CONTRADICCIÓN 1, `F0_CORPUS_AUDIT.md` §2.2.b) | **no** |
| ADR-006 | n8n as Workflow Engine | VIGENTE | A CONFIRMAR — código actual (`atlas-mvp`) usa cron de GitHub Actions + scripts `.mts`, no vi n8n wireado |
| ADR-007 | MCP-Ready Architecture | VIGENTE (visión, Plan Maestro F8) | **no** (REFERENCIA) |
| ADR-008 | AI Provider Abstraction | VIGENTE — ver ADR-011 | sí |
| ADR-009 | Event Sourcing Ready | VIGENTE | sí |
| ADR-010 | Phase 1 Scope Reduction | **VIGENTE — decisión central** | sí |
| ADR-010-B | Piso de Contexto Mínimo | VIGENTE | sí |
| ADR-010-C | Piso de Seguridad y Costo Liviano | VIGENTE | sí |
| ADR-010-D | Piso de Logging de Performance de Contenido | VIGENTE | sí |
| **ADR-010-E** | **Mandato del Panel para Alcance Fase 1 y Secuencia F2→F3** *(nuevo, F0)* | VIGENTE | sí |
| ADR-011 | Claude as Primary AI Provider | VIGENTE | sí |
| ADR-011-A | Free Provider Testing Override | VIGENTE | sí |
| ADR-012 | Agentic Discoverability (MCP) Phase 2 Priority | VIGENTE (visión, Fase 2) | **no** (REFERENCIA) |
| ADR-013 | App Rewrite on DOC-0032 Data Model | VIGENTE | sí |
| ADR-014 | Techo de Precio Externo y Contexto Macro | VIGENTE | sí |
| ADR-015 | Competitor Scout Automático (Escala Fase 2) | VIGENTE | sí |

## RFC — Propuestas Funcionales

| ID | Título | Estado | Gobierna |
|---|---|---|---|
| RFC-0001 | AI-Native Architecture | VIGENTE (tesis fundacional) | sí (a nivel de principios) |
| RFC-0002 | AI Orchestration | REFERENCIA — diferido por ADR-010 | **no** |
| RFC-0003 | Memory System | REFERENCIA — diferido por ADR-010 | **no** |
| RFC-0004 | Multi-Agent System | REFERENCIA — diferido por ADR-010 | **no** |
| RFC-0005 | Workflow Orchestration | VIGENTE | A CONFIRMAR — mismo motivo que ADR-006 (n8n) |
| RFC-0006 | Hospitality Domain Model | VIGENTE | sí |
| RFC-0007 | Direct Booking Engine | REFERENCIA — Plan Maestro lo ubica en F8 | **no** |
| RFC-0008 | Revenue Intelligence System | **VIGENTE — doc central de F1** | sí |
| RFC-0009 | Growth Intelligence System | REFERENCIA — Plan Maestro lo ubica en F9 | **no** |
| RFC-0010 | AI Tooling and Execution Layer | VIGENTE | sí |

## ARCH — Catálogo de Arquitectura Técnica

| ID | Título | Estado | Gobierna |
|---|---|---|---|
| ARCH-000 | Architecture Overview | VIGENTE | sí |
| ARCH-002 | High Level Architecture | VIGENTE | sí |
| ARCH-003 | System Components and Runtime (v2.0) | VIGENTE — reemplaza v1.0 archivada | sí |
| ARCH-004 | Domain Model | VIGENTE | sí |
| ARCH-005 | Domain Services | VIGENTE | sí |
| ARCH-006 | Event Bus | VIGENTE | A CONFIRMAR |
| ARCH-007 | Data Flow | VIGENTE | sí |
| ARCH-008 | API Gateway | VIGENTE | A CONFIRMAR — Next.js API routes directos, sin gateway formal verificado |
| ARCH-009 | Security Model | VIGENTE | sí |
| ARCH-010 | Scalability | VIGENTE (visión) | A CONFIRMAR |
| ARCH-0012 | AI Orchestration Implementation | REFERENCIA — diferido por ADR-010 | **no** |
| ARCH-0013 | Memory Architecture | REFERENCIA — diferido por ADR-010 | **no** |
| ARCH-0014 | Multi-Agent System Implementation | REFERENCIA — diferido por ADR-010 | **no** |
| ARCH-0015 | Event-Driven Architecture | VIGENTE | A CONFIRMAR |
| ARCH-0016 | API Gateway and Security Layer | VIGENTE | A CONFIRMAR |
| ARCH-0018 | MVP Implementation Roadmap | REFERENCIA — probable superseded en la práctica por `ATLAS_PLAN_MAESTRO_SAAS.md` §7 (no declarado formalmente, ver `F0_CORPUS_AUDIT.md` §2.4) | **no** |
| ARCH-0019 | Product Roadmap and Evolution | REFERENCIA — mismo motivo; **drift interno**: el doc se autodeclara "Documento 20" (ver auditoría §2.1.b) | **no** |
| ARCH-0020 | Master System Specification (v2) | REFERENCIA — roadmap de visión | **no** |
| ARCH-0021 | AI Execution Levels | VIGENTE | sí |
| ARCH-0022 | Business Decision Validation Layer (BDVL) | VIGENTE | sí |
| ARCH-0023 | Operational UX Strategy | VIGENTE | sí |
| ARCH-0024 | State Consistency Model | VIGENTE | sí |
| ARCH-0025 | MVP Physical Architecture | VIGENTE | sí |
| ARCH-0026 | Workflow Responsibility Model | VIGENTE | A CONFIRMAR — mismo motivo que ADR-006 (n8n) |
| ARCH-0027 | AI Cost Optimization Strategy | VIGENTE | sí |
| ARCH-0028 | AI Safety and Guardrails | VIGENTE | sí |
| ARCH-0029 | Context Retrieval Strategy | VIGENTE | A CONFIRMAR — sin leer a fondo en F0; puede ser Fase 2 (Context Builder) o relacionar con ADR-010-B (piso mínimo, ya activo) |
| ARCH-0030 | AI Model Routing and Provider Strategy | VIGENTE | sí |

## DOC — Namespace Secundario

| ID | Título | Estado | Gobierna |
|---|---|---|---|
| DOC-0011 | Database Architecture | VIGENTE | A CONFIRMAR — posible superseded parcial por DOC-0032, no verificado |
| DOC-0017 | Deployment and Infrastructure Model | VIGENTE | A CONFIRMAR |
| DOC-0031 | Security and Multi-Tenancy Handbook | VIGENTE | sí — **candidato a "DOC-0061"** citado por la tarea F0 (no existe con ese ID, ver auditoría §2.2.a); pendiente confirmación de Denis |
| DOC-0032 | Data Model Specification — Fase 1 | VIGENTE | sí |
| DOC-0033 | Testing Strategy — Fase 1 | VIGENTE | sí |

## Specs y docs canónicos sin ID formal (citados por nombre)

| Nombre | Tipo | Estado | Gobierna |
|---|---|---|---|
| DOMAIN_KNOWLEDGE.md | SPEC | VIGENTE — lectura obligatoria (CLAUDE.md) | sí |
| DOMAIN_KNOWLEDGE old.md | — | **HAZARD** — mismo título/fecha que el canónico, sin trackear, sin referenciar desde ningún otro doc; no movido ni borrado en F0 (fuera de scope) | no aplica |
| SPEC-PRICING-MODEL.md | SPEC | VIGENTE (Draft — 2 puntos pendientes con el propietario) | sí |
| SPEC-PROACTIVE-PRICING-MODEL.md | SPEC | VIGENTE — lectura obligatoria (CLAUDE.md) | sí |
| manual_revenue_pricing.md | SPEC | VIGENTE — lectura obligatoria (CLAUDE.md) | sí |
| ONBOARDING-COMPETITOR-SOURCES.md | SPEC | VIGENTE | sí |
| ESTADO_PROYECTO.md | TABLERO | VIGENTE — tablero operativo, no fuente de reglas | no (informativo) |
| ATLAS_PLAN_MAESTRO_SAAS.md | PLAN | VIGENTE — plan de fases operativo actual (F0–F9); **no estaba trackeado en git al iniciar F0**; contiene 5 referencias stale a "ADR-011" (ver auditoría §2.4, no corregidas — fuera de scope) | sí |
| MASTER_PLAN_CURADO_POST_HOSPIOS.md | PLAN | A CONFIRMAR — posible predecesor superseded de `ATLAS_PLAN_MAESTRO_SAAS.md` (esquemas de fase incompatibles, ninguno declara al otro superseded — ver auditoría §2.4) | no (por ahora) |
| GAP_ANALYSIS.md | REFERENCIA | REFERENCIA — análisis puntual 2026-07-11 | no |
| ATLAS_MOAT_AGENTICO_TESIS.md | REFERENCIA | REFERENCIA — tesis estratégica | no |
| BUSINESS_CASE_AND_FINANCIAL_MODEL.md | REFERENCIA | REFERENCIA (Draft financiero) | no |
| CHANGELOG_RECONCILIATION.md | REFERENCIA | REFERENCIA — histórico de curación 2026-07-03 | no |
| 00_INDICE_MAESTRO.md | ÍNDICE | **SUPERSEDIDO-por-docs/INDEX.md** (este documento) | no |
| F0_CORPUS_AUDIT.md | AUDITORÍA | VIGENTE — resultado de esta misma tarea F0 | no aplica (es el respaldo de este índice) |

## SKILLS (prompts de agente — categoría aparte, no ADR/RFC/ARCH/DOC)

| Archivo | Tipo | Estado | Gobierna |
|---|---|---|---|
| SKILLS/architecture.md, decision-engine.md, engineering.md, hospitality.md, quality.md | SKILL | VIGENTE (asumido — no leídos a fondo en F0) | A CONFIRMAR |

## _ARCHIVE

Todo lo superseded preservado por trazabilidad — no se listó fila por fila (19 archivos según `00_INDICE_MAESTRO.md` §7). Fuera de citación activa por definición.

---

## Ítems A CONFIRMAR — resumen para Denis

Priorizados por impacto en F1:

1. **`DOC-0061`** no existe — ¿la intención era `DOC-0031`? (candidato propuesto, sin contradicción encontrada contra los otros 5 docs gobernantes de F1).
2. **n8n**: ADR-006, RFC-0005, ARCH-0026, ARCH-0006, ARCH-0015, ARCH-0016, ARCH-0008 — ¿algo de esto gobierna código real hoy, o el n8n de ADR-006 quedó sin implementar (cron + scripts lo reemplazó de facto)? Afecta a 7 filas de esta tabla.
3. **`ARCH-0029`** (Context Retrieval Strategy) — ¿describe el Context Builder completo (Fase 2, REFERENCIA) o algo que ADR-010-B ya usa parcialmente (gobierna F1)?
4. **Doble roadmap**: `MASTER_PLAN_CURADO_POST_HOSPIOS.md` vs `ATLAS_PLAN_MAESTRO_SAAS.md` — ¿el segundo reemplaza al primero?
5. **`DOMAIN_KNOWLEDGE old.md`** — ¿backup a archivar, o tiene contenido divergente que reconciliar antes de que F1 cite `DOMAIN_KNOWLEDGE.md` a ciegas?

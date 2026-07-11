# ESTADO DEL PROYECTO ATLAS — Tablero Único de Verdad

**Última actualización:** 2026-07-11
**Mantenimiento:** este archivo se actualiza al cerrar CUALQUIER tarea, sin pedirlo (instrucción del founder). Es la vista de secuencialidad y objetivos.
**Fuente de verdad de decisiones:** los ADR/DOC referenciados en cada fila. Si una fila y su ADR se contradicen, gana el ADR y se corrige la fila.

## Cómo leer

- **Estados:** `HECHO` · `EN CURSO` · `PENDIENTE` · `DIFERIDO` (a fase posterior, con ADR que lo respalda).
- **Fases** (marco operativo de los ADR, no las Etapas 0-7 del roadmap ARCH-0019):
  - **Fase 0** — Fundación: documentación curada, data model, infraestructura, seguridad.
  - **Fase 1** — Revenue Agent únicamente (ADR-010). Scope activo.
  - **Fase 2** — Context Builder, Brains, Multi-Agent, Booking, Growth, Competitor Scout.
  - **Fase 3+** — Visión completa NeuralCore (RFC-0001..0010).
- **Verificación:** ningún ítem se marca `HECHO` sin evidencia real (migración aplicada, fila en DB, test verde, output real). Ver política en CLAUDE.md.

---

## FASE 0 — Fundación

| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Reconciliación documental (69 origen → 53 canónicos + 17 archivados) | HECHO | 2026-07-03 | CHANGELOG_RECONCILIATION.md |
| Data Model Specification | HECHO | 2026-07-03 | DOC-0032 |
| Security & Multi-Tenancy Handbook | HECHO | 2026-07-03 | DOC-0031 |
| Testing Strategy | HECHO | 2026-07-03 | DOC-0033 |
| Proyecto Supabase `xlhafthdfbrisvxbowjt` (atlas-fase1) | HECHO | 2026-07-04 | CLAUDE.md (infra) |
| 14 migraciones + RLS en toda tabla + Custom Access Token Hook (claim `organization_id`) | HECHO | 2026-07-04 | DOC-0032, DOC-0031 §3 |
| Decisión de rewrite sobre DOC-0032 | HECHO | 2026-07-04 | ADR-013 |
| Retiro formal de la app vieja (Gemini + Pages Router) a `_ARCHIVE` | HECHO | 2026-07-08 | ADR-013 AI#4 |

---

## FASE 1 — Revenue Agent (scope activo)

### Núcleo construido
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Auth + membership (@supabase/ssr, cookies) | HECHO | 2026-07-05 | DOC-0031 §4 |
| CRUD properties + units (`/console`, `/console/properties`) | HECHO | 2026-07-05 | DOC-0032 |
| Capa de mensajería abstracta (Channel) + adapter Telegram | HECHO | 2026-07-06 | ADR-003-A |
| Credenciales de canal cifradas (AES-256-GCM) en `property_integrations` | HECHO | 2026-07-06 | DOC-0031 §4 |
| Revenue Agent: contexto mínimo → circuit breaker → L3 → BDVL → execution_logs | HECHO | 2026-07-07 | ADR-010-B, ARCH-0027, ADR-010-C, ARCH-0028 |
| Abstracción de proveedor IA (Claude/Groq/Gemini vía `REVENUE_AGENT_PROVIDER`) | HECHO | 2026-07-07 | ADR-008, ADR-011, ADR-011-A |
| BDVL como policy engine (riesgo por % cambio; nunca auto-aplica) | HECHO | 2026-07-07 | ADR-010-C, ARCH-0022 |
| Comandos Telegram: `/pricing`, `/aprobar`, `/rechazar`, `/disponibilidad` | HECHO | 2026-07-07 | ADR-003-A, ARCH-0028 |
| Disponibilidad: intent L1 + query L0 determinística (capacidad + solape reservas) | HECHO | 2026-07-07 | ADR-010-B |
| Ingesta histórica real (reservas CSV/JSON + Excel, dólar blue por año) | HECHO | 2026-07-08 | ADR-011-A (fx.ts) |
| Pricing de **ambas unidades** cuando no se especifica | HECHO | 2026-07-08 | — (mejora Fase 1) |
| Competidores por unidad con comparabilidad (`applies_to_unit_id`, `adjustment_note`) | HECHO | 2026-07-08 | ADR-015 (carga manual), DOC-0031 §3 |
| Transparencia en recomendación (competidor: nombre+precio+link+unidad+nota) | HECHO | 2026-07-08 | — (mejora Fase 1) |
| Guardrail no-inventar m²/características (usa nota o compara crudo y lo aclara) | HECHO | 2026-07-08 | ARCH-0028 |
| Fix: pricing COMPARA base vs competidores (ajustados) y recomienda subir/bajar/mantener — ya no repite el base | HECHO | 2026-07-11 | RFC-0008 (bug de instrucción, evidencia en dump-prompt.mts) |
| Ingesta de competidores reales de LindaBay (10 filas cargadas) | HECHO | 2026-07-08 | ADR-015 |
| Webhook Telegram serverless (Vercel) reemplaza polling | HECHO | 2026-07-08 | ADR-003-A |
| Deploy a Vercel (atlas-mvp-pink.vercel.app) + webhook registrado | HECHO | 2026-07-08 | DOC-0017 |
| Tests de lógica de negocio (Vitest, 15 verdes) | HECHO | 2026-07-08 | DOC-0033 |
| Round-trip verificado en producción (execution_logs + bdvl_decisions reales) | HECHO | 2026-07-08 | ADR-010-C |

### Abierto en Fase 1
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Techo de precio: ajuste **manual** del propietario al confirmar (BDVL requires_confirmation) | EN CURSO | — | ADR-014 (Fase 1 vigente) |
| Dashboard web básico (existe login + console + CRUD; interfaz principal es Telegram) | EN CURSO | 2026-07-05 | ADR-010 (scope) |
| Unificar etiqueta `source` en pricing_history (`ai_recommendation` vs `agent`/`manual`) | PENDIENTE | — | issue spawneado (task_07321159) |
| Criterio de salida Fase 1 → Fase 2 (métrica de LindaBay que dispara Context Builder) | PENDIENTE | — | ADR-010 AI#3 |
| Actualizar ARCH-0018 (roadmap) con el scope Fase 1 explícito | PENDIENTE | — | ADR-010 AI#4 |

### Divergencias registradas (implementación ≠ ADR original, ver GAP_ANALYSIS)
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Scraper de competidores en vivo: ADR-010 lo listaba en Fase 1 → diferido a Fase 2 (carga manual) | DIFERIDO | 2026-07-08 | ADR-015 (supersede parcial de ADR-010) |
| Workflow engine: ADR-006 manda n8n; Fase 1 usa Next.js + webhook Vercel, **sin n8n** | PENDIENTE (formalizar) | — | ADR-006 (sin ADR que registre la divergencia — ver GAP) |

---

## FASE 2 — Diferido (con registro)

| Ítem | Estado | Respaldo |
|---|---|---|
| AI Orchestrator | DIFERIDO | ADR-010, RFC-0002 / ARCH-0012 |
| Context Builder (context real, no piso mínimo) | DIFERIDO | ADR-010, ARCH-0029 |
| Property Brain / Guest Brain / Growth Brain (memoria vectorial) | DIFERIDO | ADR-010, RFC-0003 / ARCH-0013 |
| Multi-Agent System completo | DIFERIDO | ADR-010, RFC-0004 / ARCH-0014 |
| BDVL como capa formal de validación (hoy solo logging + policy) | DIFERIDO | ADR-010, ARCH-0022 |
| Competitor Scout automático (scraping OTAs + matching + Decision Graph) | DIFERIDO | ADR-015, RFC-0008 |
| Techo de precio automático + señal macro estacional | DIFERIDO | ADR-014 |
| WhatsApp como canal (Telegram es interino) | DIFERIDO | ADR-003-A |
| Direct Booking Engine + Payments (MercadoPago) | DIFERIDO | RFC-0007, ARCH-0019 Etapa 6 |
| Growth Intelligence (generación de contenido, marketing ABC1) | DIFERIDO | RFC-0009 (tablas content_* existen como logging floor, ADR-010-D) |
| Descubribilidad agéntica / MCP tooling | DIFERIDO | ADR-012, ADR-007, RFC-0010 |
| Decision Graph (aprende de aprobaciones/rechazos del dueño) | DIFERIDO | ADR-013, ADR-015 |

---

## FASE 3+ — Visión completa NeuralCore

| Ítem | Estado | Respaldo |
|---|---|---|
| Plataforma multi-módulo AI-native completa (Etapas 4-7 del roadmap) | DIFERIDO | ARCH-0019, RFC-0001..0010 |
| Protocol Rails / ecosistema agéntico | DIFERIDO | ARCH-0019 Etapa 7, RFC-0010 |

---

## Documentos de ingeniería aún no escritos (backlog conocido, no perdido)

| Doc | Estado | Nota |
|---|---|---|
| API Spec (OpenAPI) | PENDIENTE | no bloqueante (CLAUDE.md gaps) |
| Prompt Engineering Standards | PENDIENTE | no bloqueante |
| Coding Standards | PENDIENTE | no bloqueante |
| DOC-34 a DOC-60 (Glossary, Onboarding, Playbook, etc.) | DIFERIDO | backlog iterativo (CHANGELOG_RECONCILIATION §Sesión 2) |

# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0020
# DOCUMENT    : Master System Specification
# VERSION     : 2.0
# STATUS      : Accepted
# CLASS       : Core Architecture — Master Reference
#
# REPLACES
#
# ARCH-0020 v1.0 (archivada — sigue disponible como referencia histórica)
#
# REFERENCES
#
# Todo el corpus ADR-001 a ADR-011, RFC-0001 a RFC-0010, ARCH-000 a ARCH-0030
#
# ============================================================================

# 1. Propósito

Especificación maestra que sintetiza el estado actual y completo de la arquitectura de ATLAS. v1.0 quedó desactualizada tras la incorporación de ARCH-0021 a ARCH-0030 (Execution Levels, BDVL, Cost Optimization, Safety & Guardrails, Model Routing) y de las decisiones ADR-010, ADR-011, ADR-003-A. Este documento es el punto de entrada único para entender el sistema completo sin tener que reconstruir el mapa manualmente desde 30+ documentos individuales.

---

# 2. Misión (sin cambios respecto a v1)

Maximizar la rentabilidad de alojamientos independientes mediante un sistema operativo impulsado por IA que reduzca el trabajo operativo y mejore continuamente las decisiones (AES-00).

---

# 3. Visión (sin cambios respecto a v1)

Ser el sistema operativo de referencia para la hospitalidad independiente en LATAM, donde la IA es el núcleo del producto, no una función adicional.

---

# 4. Pilares Arquitectónicos (actualizado)

1. AI-Native — no IA como feature, IA como núcleo de ejecución
2. Event-Driven (ADR-002)
3. Multi-Agent (ADR-004), con niveles de ejecución explícitos (ARCH-0021)
4. Memoria persistente por Brain (ADR-005 / ARCH-0013)
5. Human-in-the-loop por defecto (AES-00 Principio 7)
6. **Seguro por diseño** — guardrails de IA (ARCH-0028) y aislamiento multi-tenant (DOC-0031) *(nuevo en v2)*
7. **Provider-aware por costo** — Claude para decisiones de alto impacto, modelos gratuitos para tareas de bajo riesgo (ADR-011) *(nuevo en v2)*
8. Single Source of Truth — PostgreSQL/Supabase (ADR-001)

---

# 5. Arquitectura Core (sin cambios estructurales, referencia actualizada)

Ver ARCH-002 (High Level Architecture) y ARCH-003 (System Components and Runtime, v2.0) para el detalle completo de monolito modular, fast path, event bus y outbox pattern.

---

# 6. Modelo de Inteligencia (actualizado — este es el cambio central de v2)

La v1 describía un "Intelligence Model" genérico. La v2 lo formaliza como una **matriz de tres dimensiones** que todo agente debe satisfacer:

| Dimensión | Gobernado por |
|---|---|
| ¿Qué nivel de autonomía tiene la acción? | ARCH-0021 (Execution Levels L0-L4) |
| ¿Qué proveedor de IA la ejecuta? | ADR-011 + ARCH-0030 (Model Routing) |
| ¿Qué guardrail de seguridad aplica? | ARCH-0028 (AI Safety and Guardrails) |

Ninguna función de IA se considera completamente especificada si no responde a las tres dimensiones simultáneamente.

---

# 7. Modelo de Memoria (referencia — sin cambios, ver ARCH-0013 v2.1)

Cinco memorias: Conversation Memory, Guest Brain, Property Brain, Growth Brain, Hospitality Knowledge Network. Incluye desde v2.1: Decision Graph y Política de Retención (ver ARCH-0013 Addendum).

---

# 8. Dominios de Negocio (sin cambios respecto a v1)

Ver RFC-0006 (Hospitality Domain Model) para el detalle completo.

---

# 9. Modelo de Ejecución (referencia — ver ARCH-0021 para el detalle completo de L0-L4)

---

# 10. Principios de IA (actualizado)

Se añaden a los principios de v1 (explicabilidad, agnosticismo de proveedor):

- **Ningún agente se auto-promueve de Execution Level** (ARCH-0028 §3)
- **BDVL es obligatorio antes de cualquier acción L3+** (ARCH-0022, reforzado por ADR-010 como prerequisito de Phase 1)
- **Claude es el proveedor por defecto en L2-L4; modelos gratuitos solo en L1** (ADR-011)

---

# 11. Principios de Eventos (sin cambios respecto a v1)

---

# 12. Multi-Tenancy (actualizado — ahora con documento operativo dedicado)

v1 declaraba el principio. v2 remite al manual operativo completo: **DOC-0031 (Security and Multi-Tenancy Handbook)**, que incluye el checklist de RLS obligatorio y la clase de bug ya detectada y corregida en producción (fuga de datos de competidores entre tenants).

---

# 13. Seguridad (actualizado — expansión mayor en v2)

v1 tenía una sección genérica de seguridad. v2 la reemplaza por referencia a dos documentos dedicados, nuevos en esta revisión:

- **ARCH-0028 (AI Safety and Guardrails)** — modelo de amenazas específico de IA, guardrails por Execution Level, aislamiento de prompt
- **DOC-0031 (Security and Multi-Tenancy Handbook)** — RLS operativo, arquitectura de dos niveles de API keys, testing obligatorio

---

# 14. Escalabilidad (sin cambios respecto a v1 — ver ARCH-010)

---

# 15. Baseline Tecnológico (actualizado)

| Componente | Tecnología | Decisión |
|---|---|---|
| Backend/DB | Supabase (PostgreSQL) | ADR-001 |
| Workflow engine | n8n | ADR-006 |
| Canal de mensajería | **Telegram (interino Phase 1) → WhatsApp (destino)** | ADR-003 + **ADR-003-A** *(actualizado en v2)* |
| Proveedor de IA primario | **Claude** (L2-L4) + modelo gratuito (L1) | ADR-008 + **ADR-011** *(nuevo en v2)* |
| Deployment | Vercel | ARCH-0025 |
| Arquitectura de agentes | Multi-agent con Execution Levels | ADR-004 + ARCH-0021 |

---

# 16. Estrategia de Producto y Scope Actual (nuevo en v2 — la sección más importante para el estado real del proyecto)

**Phase 1 (activo, ver ADR-010):** Revenue Agent únicamente, sin AI Orchestrator ni Context Builder ni Brains completos. BDVL logging es prerequisito obligatorio desde el inicio, aunque la capa de validación completa se difiere a Phase 2.

**Phase 2+ (documentado, no implementado):** El resto de la visión NeuralCore permanece completamente especificada en RFC-0001 a RFC-0010 y ARCH-0012 a ARCH-0030 como referencia de largo plazo — no se descarta, se secuencia correctamente esta vez.

Este es el cambio de mentalidad central respecto a v1: **la especificación completa siempre existió; lo que faltaba era la decisión explícita de qué NO construir todavía.**

---

# 17. Documentos aún pendientes (transparencia total — gaps reales, no resueltos en esta revisión)

| Doc | Prioridad | Bloquea inicio de código? |
|---|---|---|
| Data Model Specification | 🔴 Alta | Sí — necesario para escribir migraciones reales |
| API Specification (OpenAPI) | 🟡 Media | No bloquea Phase 1 (superficie de API es pequeña) |
| Coding Standards | 🟡 Media | Recomendado antes de escalar más allá de un solo desarrollador con IA |
| Prompt Engineering Standards | 🟡 Media | Recomendado antes de escribir prompts de producción del Revenue Agent |
| Testing Strategy | 🔴 Alta | Sí, dado el historial de bugs de multi-tenancy ya detectado |
| Resto de DOC-32 a DOC-60 | 🟢 Baja | No — se escriben iterativamente durante el desarrollo |

Ver recomendación de secuencia en la respuesta de curación que acompaña este corpus.

# ADR-011: Claude como Proveedor de IA Primario con Routing por Costo hacia Modelos Gratuitos

**Status:** Accepted
**Date:** 2026-07-03
**Deciders:** Denis Gueler (founder)
**Amends:** ADR-008 (AI Provider Abstraction), ARCH-0030 (AI Model Routing)

## Context

ADR-008 estableció una capa de abstracción agnóstica de proveedor de IA. Esa decisión sigue vigente — no se reemplaza. Lo que este ADR fija es la **política de routing dentro de esa abstracción**, ahora que existe una suscripción activa de Claude.

Sin una política explícita, el riesgo es usar Claude indiscriminadamente (costo innecesario en tareas triviales) o subutilizarlo (degradar calidad en decisiones de negocio de alto impacto por ahorrar costo donde no corresponde).

## Decision

Se adopta un **modelo de routing por nivel de ejecución** (ARCH-0021), no por tipo de tarea aislada:

| Execution Level | Proveedor por defecto | Justificación |
|---|---|---|
| L0 — Determinístico | Ninguno (reglas) | No aplica IA |
| L1 — IA Simple (clasificación, extracción, tagging) | **Modelo gratuito/open-source** (routing ARCH-0030) | Bajo riesgo, alto volumen — el costo de Claude no se justifica |
| L2 — IA Contextual (conversación con huésped) | **Claude** | Calidad de conversación afecta directamente la experiencia del huésped y la marca |
| L3 — IA Analítica (pricing, revenue) | **Claude** | Decisión de negocio de alto impacto — requiere el modelo de mayor confiabilidad del stack |
| L4 — IA Estratégica (multi-propiedad, Growth Brain) | **Claude** | Máximo impacto, mínima tolerancia a error |

**Regla dura:** un modelo gratuito nunca es el último eslabón antes de una decisión L3/L4 visible al usuario. Puede pre-procesar (extracción, resumen) pero la decisión final pasa por Claude.

## Options Considered

### Option A: Claude para todo
| Dimensión | Evaluación |
|---|---|
| Calidad | Máxima y uniforme |
| Costo | Alto — penaliza tareas L1 de alto volumen (clasificación de mensajes, tagging) sin beneficio de calidad marginal |
| Complejidad | Mínima (un solo proveedor) |

### Option B: Routing por Execution Level (elegida)
| Dimensión | Evaluación |
|---|---|
| Calidad | Máxima donde importa (L2-L4), suficiente donde no (L1) |
| Costo | Optimizado — alineado con ARCH-0027 |
| Complejidad | Media — requiere que ARCH-0030 tenga tabla de routing explícita y testeada |

### Option C: Routing dinámico por confianza del modelo (self-assessment)
| Dimensión | Evaluación |
|---|---|
| Calidad | Variable — depende de que el modelo barato "sepa" que no sabe |
| Costo | Impredecible |
| Complejidad | Alta, y viola ARCH-0028 §7 (un modelo gratuito no debe autoevaluarse para decidir su propia promoción a L3/L4) |

## Trade-off Analysis

Option B es la única consistente con ARCH-0028 (guardrails ya definen que un modelo gratuito nunca llega solo a L3/L4). Option A es más simple pero desperdicia presupuesto en tareas L1 de alto volumen (ej. clasificación de cada mensaje entrante de Telegram/WhatsApp, tagging de reviews scrapeados). Option C introduce riesgo de seguridad ya vetado por ARCH-0028.

## Consequences

**Facilita:**
- Presupuesto de IA predecible — el gasto en Claude se concentra en L2-L4, proporcional al valor generado
- Calidad consistente en todo lo que el huésped/propietario ve directamente

**Dificulta:**
- Requiere que ARCH-0030 mantenga actualizada la tabla Execution Level → Proveedor conforme se agregan agentes nuevos
- Introduce una dependencia de disponibilidad de al menos dos proveedores (Claude + fallback gratuito) — si Claude tiene outage, L2-L4 deben tener plan de degradación (definir en ARCH-0030)

**A revisar:**
- Qué modelo gratuito específico se usa para L1 (Llama vía Groq, Gemini free tier, u otro) — decisión operativa, no arquitectónica, se resuelve en ARCH-0030 con criterio de costo/latencia al momento de implementar

## Action Items
1. [ ] Actualizar ARCH-0030 con tabla de routing explícita por Execution Level (no solo por tarea)
2. [ ] Definir plan de degradación si Claude no está disponible (fallback a L2 con modelo gratuito + flag de "calidad reducida" visible internamente)
3. [ ] Instrumentar costo por proveedor por Execution Level para validar la hipótesis de ahorro (ARCH-0027)

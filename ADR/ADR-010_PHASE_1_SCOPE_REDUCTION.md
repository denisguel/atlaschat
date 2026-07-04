# ADR-010: Reducción de Alcance a Phase 1 — Revenue Agent Únicamente

**Status:** Accepted
**Date:** 2026-07-03
**Deciders:** Denis Gueler (founder)
**Relates to:** ARCH-0018 (MVP Implementation Roadmap), ARCH-0019 (Product Roadmap), RFC-0008 (Revenue Intelligence)

## Context

La visión completa de ATLAS (NeuralCore, plataforma multi-módulo AI-native) está documentada en el corpus completo (RFC-0001 a RFC-0010, ARCH-0012 a ARCH-0030). La construcción del MVP (`atlas-mvp`) intentó implementar un componente de Fase 3 (Revenue Agent con inteligencia de pricing) **sin las fundaciones de Fase 2** (AI Orchestrator, Context Builder, Property Brain).

Root cause ya identificado en auditoría técnica previa: esta secuenciación incorrecta produjo directamente los síntomas reportados — recomendaciones genéricas, ausencia de aprendizaje, cero interactividad conversacional real. Esta decisión nunca fue formalizada como ADR pese a ser la decisión arquitectónica más importante del proyecto hasta la fecha.

## Decision

Se reduce el alcance de **Phase 1** exclusivamente a:

- **Revenue Agent** como único agente activo
- Integración con Telegram (ver ADR-003-A) como canal de interacción
- Scraper de precios de competidores
- Dashboard básico de visualización

**Explícitamente fuera de Phase 1** (diferido a Phase 2+):
- AI Orchestrator (RFC-0002 / ARCH-0012)
- Context Builder (parte de ARCH-0013 / ARCH-0029)
- Property Brain, Guest Brain, Growth Brain (RFC-0003 / ARCH-0013)
- Multi-Agent System completo (RFC-0004 / ARCH-0014)
- BDVL como capa formal de validación (ARCH-0022) — *aunque el logging de BDVL es prerequisito duro antes de cualquier loop de aprendizaje, ver Action Items*

La documentación de visión completa (NeuralCore) se preserva íntegramente como referencia de largo plazo — no se descarta, se pausa su implementación.

## Options Considered

### Option A: Continuar expandiendo sobre la base actual de atlas-mvp
| Dimensión | Evaluación |
|---|---|
| Velocidad | Alta en el corto plazo |
| Deuda técnica | Crítica — repite el error de secuenciación ya diagnosticado |
| Riesgo | Alto — cualquier feature nueva hereda la ausencia de Context Builder |

### Option B: Reconstruir Phase 1 con secuenciación correcta (elegida)
| Dimensión | Evaluación |
|---|---|
| Velocidad | Media — requiere reescritura del Revenue Agent sobre fundaciones mínimas |
| Deuda técnica | Baja — BDVL logging desde el día uno evita repetir el error |
| Riesgo | Bajo — scope explícitamente acotado y documentado |

### Option C: Implementar Phase 2 completa antes de cualquier agente
| Dimensión | Evaluación |
|---|---|
| Velocidad | Muy baja — sin validación de mercado por meses |
| Deuda técnica | Mínima |
| Riesgo | Alto riesgo de negocio — sin producto usable, sin aprendizaje de usuarios reales |

## Trade-off Analysis

Option B es el punto medio correcto: evita repetir el error de secuenciación (Option A) sin caer en over-engineering previo a validación de mercado (Option C). La clave es que **BDVL logging (aunque no la capa de validación completa) es innegociable desde el inicio** — sin logging estructurado de decisiones desde el día uno, cualquier intento futuro de Phase 2 (aprendizaje) carece de datos históricos con los que entrenar o validar.

## Consequences

**Facilita:**
- Un MVP con alcance realista, testeable con LindaBay (caso de validación real) en semanas, no meses
- Base de datos de decisiones (BDVL logs) acumulándose desde el primer día, lista para Phase 2
- Claridad para cualquier colaborador futuro sobre qué SÍ y qué NO se está construyendo ahora

**Dificulta:**
- El Revenue Agent de Phase 1 tendrá límites conocidos (sin memoria persistente real, sin conversación multi-turno rica) — esto es aceptado, no es un bug, es el scope
- Reescritura de lo ya construido en atlas-mvp que no respete esta secuenciación

**A revisar:**
- Criterio de salida de Phase 1 → Phase 2 (qué métrica de LindaBay dispara la inversión en Context Builder/Property Brain)

## Action Items
1. [ ] Implementar BDVL logging mínimo (tabla de decisiones + input/output/regla aplicada) antes de escribir cualquier línea nueva del Revenue Agent
2. [ ] Auditar atlas-mvp existente: qué código es reutilizable bajo este scope reducido vs qué debe reescribirse
3. [ ] Definir criterio de salida de Phase 1 (métrica concreta de LindaBay que justifique invertir en Phase 2)
4. [ ] Actualizar ARCH-0018 (MVP Implementation Roadmap) para reflejar este scope explícitamente

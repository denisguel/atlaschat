# ADR-011-A: Enmienda — Override de Proveedor Gratuito para L3 durante Testing (config-gated)

**Status:** Accepted
**Date:** 2026-07-05
**Amends:** ADR-011 (Claude as Primary AI Provider)
**Relates to:** ADR-008 (AI Provider Abstraction), ARCH-0027 (Cost Optimization), DOC-0032/DOC-0033
**Deciders:** Denis Gueler (founder)

## Context

ADR-011 fija Claude como proveedor de IA para Execution Levels L2-L4, y prohíbe
que un modelo gratuito sea el último eslabón antes de una decisión L3/L4 visible
al usuario. Esa decisión sigue siendo la política de **producción**.

Durante la validación de Phase 1 con LindaBay, la cuenta de Anthropic quedó sin
saldo (la API responde `400 credit balance too low`). Cargar saldo a Claude
antes de terminar de validar la **arquitectura** del Revenue Agent no aporta —
el objetivo del checkpoint era probar el circuito completo (contexto mínimo →
razonamiento → BDVL → propietario, sin auto-aplicarse), no la calidad final del
modelo. Se necesita razonamiento L3 real (un LLM que use el contexto inyectado),
pero no necesariamente Claude, para cerrar ese checkpoint sin costo.

La capa de proveedor ya está abstraída (ADR-008): `PricingProvider` con
implementaciones intercambiables (Claude / Groq / Gemini), seleccionadas por la
variable de entorno `REVENUE_AGENT_PROVIDER`.

## Decision

Se permite un **override temporal y config-gated** del proveedor L3 hacia un
modelo gratuito (Groq o Gemini) **exclusivamente durante testing**:

- El override es **solo configuración**: `REVENUE_AGENT_PROVIDER=groq|gemini`.
  No hay cambio de código para activarlo ni para revertirlo.
- **ADR-011 sigue vigente sin cambios.** El default del código es `claude`; el
  override no altera la decisión de producción. Groq/Gemini NO son la última
  palabra de L3/L4 en producción.
- **Auditabilidad obligatoria:** el proveedor realmente usado en cada invocación
  queda registrado en `execution_logs.provider` (ej. `groq:llama-3.3-70b-versatile`
  vs `claude:claude-opus-4-8`). No hay ambigüedad sobre qué modelo produjo cada
  recomendación.
- El override no debilita ningún otro guardrail: BDVL, circuit breaker de costo
  (ARCH-0027) y la regla de "el agente PROPONE, no ejecuta" (ARCH-0028) aplican
  igual, sea cual sea el proveedor.

## Criterio de reversión

Cuando LindaBay esté validada y se cargue saldo a Claude, se setea
`REVENUE_AGENT_PROVIDER=claude` (o se remueve la variable, ya que `claude` es el
default). Con eso, producción vuelve a operar bajo ADR-011 sin ningún cambio de
código. Esta enmienda queda entonces como override **inactivo** — se conserva la
capacidad (útil para tests de integración baratos o fallback futuro), pero no se
usa como camino de decisión de producción.

## Options Considered

### Option A: Cargar saldo a Claude ahora y usarlo en testing
| Dimensión | Evaluación |
|---|---|
| Costo | Gasto inmediato antes de validar arquitectura — prematuro |
| Fidelidad de testing | Máxima, pero innecesaria para validar el circuito |
| Riesgo | Bajo, pero gasta capital de validación en la pieza equivocada |

### Option B: Override a modelo gratuito, config-gated, temporal (elegida)
| Dimensión | Evaluación |
|---|---|
| Costo | Cero durante testing |
| Fidelidad de testing | Suficiente — valida contexto→razonamiento→BDVL end-to-end |
| Reversión | Trivial (1 env var), sin reescritura (ADR-008) |
| Riesgo de deuda silenciosa | Mitigado por esta ADR + auditoría en execution_logs |

### Option C: Usar el fallback determinístico (reglas, sin LLM) para testing
| Dimensión | Evaluación |
|---|---|
| Costo | Cero |
| Fidelidad de testing | Insuficiente — no ejercita razonamiento de un LLM sobre el contexto; el fallback ya existe para degradación, no para validar el camino L3 |

## Trade-off Analysis

Option B valida exactamente lo que el checkpoint requería (un LLM real razonando
sobre el piso de contexto mínimo, clasificado por BDVL, propuesto sin
auto-aplicarse) a costo cero, y es reversible con una variable gracias a la
abstracción de ADR-008. El único riesgo real —que "gratuito para L3" se filtre a
producción y contradiga ADR-011 en silencio— se neutraliza con dos mecanismos:
el default de código es `claude`, y `execution_logs.provider` audita el proveedor
real de cada recomendación. Option A gasta capital antes de tiempo; Option C no
prueba el camino L3.

## Consequences

**Facilita:**
- Cierre del checkpoint de Phase 1 sin cargar saldo, con evidencia real
- Prueba de que la abstracción de proveedor (ADR-008) funciona: 3 proveedores
  intercambiables por config

**Dificulta:**
- Introduce un modo de operación (proveedor gratuito en L3) que NO debe llegar a
  producción — requiere disciplina de config y la reversión documentada arriba

**A revisar:**
- Confirmar, antes del primer uso productivo real con huéspedes, que
  `REVENUE_AGENT_PROVIDER` está en `claude` (o ausente) y que hay saldo Claude

## Action Items
1. [ ] Al validar LindaBay + cargar saldo Claude: `REVENUE_AGENT_PROVIDER=claude` (o remover la var)
2. [ ] Chequeo pre-producción: `execution_logs.provider` debe mostrar `claude:*` en L3/L4, nunca `groq:*`/`gemini:*`
3. [ ] Mantener la abstracción `PricingProvider` (ADR-008) como el único punto de selección de proveedor

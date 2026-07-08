# ADR-014: Techo de Precio Externo y Contexto Macro Argentino para Pricing

**Status:** Proposed (borrador — NO implementar todavía)
**Date:** 2026-07-08
**Deciders:** Denis Gueler (founder)
**Relates to:** ADR-010 (Phase 1 Scope), ADR-010-B (Minimum Context Floor), ADR-011-A (Free Provider / blue por año), ARCH-0028 (Guardrails), DOC-0032 (Data Model)
**Defers to:** Fase 2

## Context

El pricing de un alquiler temporario en Argentina tiene un **techo invisible** que
el histórico local NO captura:

1. **Costo de destinos alternativos.** A cierto precio en USD, al huésped le
   conviene irse a otro destino (ej. Brasil: playa + tipo de cambio + paquetes).
   Ese es un techo de demanda externo a los datos de LindaBay y sus competidores
   directos de Villa Gesell / Mar de las Pampas.
2. **Distorsión del ciclo macro.** El precio real (en USD) está fuertemente
   afectado por el ciclo dólar / costo de servicios / salarios. Un mismo nivel de
   demanda produce precios nominales muy distintos según el momento del ciclo —
   por eso convertir ARS→USD con el blue **del año** (ya hecho en el ingestor,
   ADR-011-A / `lib/ingest/fx.ts`) es necesario pero no suficiente: captura el
   nivel de tipo de cambio, no la elasticidad de la demanda frente a destinos
   sustitutos.

El Revenue Agent de Fase 1 razona sobre **histórico local + competidores
locales** (ADR-010-B). Eso es correcto como piso, pero puede recomendar un precio
que, aunque coherente con el histórico y la competencia local, esté **por encima
del techo externo** — y vaciar la unidad porque el huésped elige Brasil.

## Decision (diferida a Fase 2)

Se propone que en Fase 2 el Revenue Agent incorpore:

1. **Techo de precio configurable por el propietario.** Un valor (por unidad y/o
   por temporada) que el dueño setea: *"no recomendar por encima de X USD/noche,
   porque a ese precio el huésped se va a Brasil"*. El agente **nunca** propone
   por encima del techo; si su razonamiento sugiere más, lo reporta como "el
   mercado histórico daría más, pero el techo externo lo limita a X" (explicable,
   ARCH-0028). Es un guardrail de demanda, no un dato histórico.

2. **Señal de tipo de cambio real por temporada.** Ya parcialmente capturada en el
   ingestor (blue por año, ADR-011-A). En Fase 2 se extendería a una señal
   estacional/actual (no solo anual) para que el agente entienda dónde está el
   ciclo al momento de preciar, no solo dónde estuvo históricamente.

**Fase 1 (vigente):** el agente usa histórico + competidores locales; el
**propietario ajusta el techo manualmente** al revisar la recomendación (que ya
pasa por BDVL `requires_confirmation`, así que el humano tiene el control). No se
implementa el techo automático ni la señal macro todavía.

## Options Considered

### Option A: Ignorar el techo externo (status quo indefinido)
| Dimensión | Evaluación |
|---|---|
| Simplicidad | Alta |
| Riesgo de negocio | El agente puede recomendar precios que vacían la unidad (huésped se va a Brasil) sin que el dato local lo advierta |

### Option B: Techo configurable por propietario + señal macro (elegida, diferida)
| Dimensión | Evaluación |
|---|---|
| Fidelidad al mercado real | Alta — incorpora la restricción externa que el histórico no ve |
| Costo | Requiere nueva config (techo por unidad/temporada) + fuente de señal macro; no bloqueante para Fase 1 |
| Control humano | El techo lo pone el dueño (conoce el mercado), el agente lo respeta |

### Option C: Modelar el destino sustituto (ej. scrapear precios de Brasil)
| Dimensión | Evaluación |
|---|---|
| Fidelidad | Máxima teórica |
| Costo/complejidad | Alto — scraping de otro mercado, tipo de cambio cruzado, estacionalidad brasileña. Sobredimensionado para el estadio actual |

## Trade-off Analysis

Option B captura el 80% del valor (el techo real de demanda) con el 20% del costo:
el propietario ya "sabe" a qué precio pierde contra Brasil — basta con dejarle
configurarlo y que el agente lo respete de forma explicable. Option C modela lo
mismo automáticamente pero a un costo desproporcionado. Option A deja un hueco de
negocio real (recomendaciones que vacían la unidad). Se elige B, diferida a Fase 2
para no expandir el scope de Fase 1 (ADR-010).

## Consequences

**Facilita (cuando se implemente):**
- Recomendaciones acotadas por la realidad de demanda, no solo por el histórico local
- Explicabilidad: el agente puede decir "el histórico daría más, pero tu techo lo limita"

**Dificulta:**
- Requiere que el propietario mantenga el techo actualizado (input humano periódico)
- La señal macro estacional necesita una fuente confiable (a definir en Fase 2)

**A revisar:**
- Granularidad del techo (por unidad, por temporada, por rango de fechas)
- Si la señal macro se deriva del propio ingestor (blue por año → por mes) o de una fuente externa

## Action Items
1. [ ] NO implementar en Fase 1 — este ADR es borrador (Proposed)
2. [ ] Fase 2: definir el modelo de datos del techo (¿columna en `units`? ¿tabla `pricing_ceilings`?)
3. [ ] Fase 2: extender la señal de tipo de cambio del ingestor de anual a estacional
4. [ ] Mientras tanto: el propietario ajusta el techo manualmente al confirmar la recomendación (BDVL `requires_confirmation` ya lo habilita)

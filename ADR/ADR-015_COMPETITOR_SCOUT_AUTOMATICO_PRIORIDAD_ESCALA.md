# ADR-015: Competitor Scout Automático como Prioridad de Escala de Fase 2

**Status:** Proposed (borrador — NO implementar todavía)
**Date:** 2026-07-08
**Deciders:** Denis Gueler (founder)
**Relates to:** ADR-010 (Phase 1 Scope), ADR-010-B (Minimum Context Floor), ADR-014 (Techo de Precio Externo), RFC-0008 (Revenue Intelligence — Competitor Scout + Growth Brain), ARCH-0028 (Guardrails), DOC-0031 (Multi-tenancy / service_role), DOC-0032 (Data Model)
**Defers to:** Fase 2

## Context

La validación de pricing de Fase 1 funciona con **carga manual y transparente**:
el propietario ingresa sus competidores (nombre, precio, link, unidad de
referencia) y las notas de comparabilidad (`adjustment_note`); el Revenue Agent
compara contra esos datos y expone explícitamente contra qué comparó, con link
verificable (ver mejoras de Fase 1 sobre `competitor_scraper_results` y el
mensaje de Telegram). Esto es correcto y suficiente para LindaBay: el dueño
conoce su mercado (Villa Gesell / Mar de las Pampas) y sus competidores.

**El problema es de escala, no de correctitud.** El modelo manual NO escala más
allá de ~10-15 clientes:

1. Cada cliente nuevo trae propiedades y competidores **desconocidos** para el
   sistema y para el founder.
2. Identificar competidores comparables (mismo tamaño, ambientes, zona), cargar
   sus precios y mantener actualizadas las notas de comparabilidad sería
   **trabajo manual del founder por cada cliente** — un costo lineal que rompe la
   economía del producto.
3. Sin automatización, el diferencial competitivo (pricing validado contra el
   mercado real) queda atado a la capacidad operativa de una persona.

El Revenue Agent de Fase 1 razona sobre **histórico local + competidores cargados
a mano** (ADR-010-B). Es el piso correcto, pero el mecanismo que lo hace
**escalable y defendible** (moat) es la automatización del descubrimiento y la
comparación de competidores — descripta en RFC-0008 (Competitor Scout + Growth
Brain).

## Decision (diferida a Fase 2)

Se propone que en Fase 2 el sistema incorpore un **Competitor Scout automático**
que reemplace la carga manual de competidores, con cuatro capacidades:

1. **Scraping en vivo de Booking / OTAs.** Obtener precios reales de competidores
   por fecha, no un snapshot manual. (Complementa, no reemplaza, el techo externo
   de ADR-014, que sigue siendo un guardrail de demanda distinto.)

2. **Identificación automática de competidores comparables por características.**
   Dado el listing del cliente (tamaño, ambientes, ubicación, amenities), el
   sistema encuentra qué otras unidades del mercado son referencia válida —
   sustituyendo el juicio manual del dueño sobre "quién es mi competencia".

3. **Ajuste de precio por diferencias.** Normalizar el precio comparado por las
   diferencias de comparabilidad (ej. +m², un ambiente más, distancia a la playa),
   convirtiendo la `adjustment_note` manual de Fase 1 en un ajuste calculado.

4. **Decision Graph: aprender de las decisiones del dueño.** Registrar qué ajustes
   y recomendaciones el propietario **acepta o rechaza** (los datos ya empiezan a
   quedar en `bdvl_decisions` desde Fase 1) y usar ese feedback para mejorar las
   futuras identificaciones y ajustes de forma autónoma. Es el componente
   "Growth Brain" de RFC-0008: el sistema mejora solo con el uso.

**Fase 1 (vigente):** carga manual de competidores con transparencia total
(nombre + precio + link + unidad + nota de comparabilidad, todo verificable por el
dueño). El agente **nunca inventa** características de comparabilidad que no estén
cargadas (guardrail ARCH-0028); si no hay `adjustment_note`, compara contra el
precio crudo y lo aclara. No se implementa scraping, ni identificación
automática, ni Decision Graph todavía.

## Options Considered

### Option A: Mantener carga manual indefinidamente (status quo)
| Dimensión | Evaluación |
|---|---|
| Simplicidad | Alta |
| Escalabilidad | **Nula más allá de ~10-15 clientes** — costo operativo lineal del founder por cliente |
| Moat | Débil — cualquiera puede pedirle a un dueño que cargue competidores |

### Option B: Competitor Scout automático + Decision Graph (elegida, diferida)
| Dimensión | Evaluación |
|---|---|
| Escalabilidad | Alta — onboarding de cliente nuevo sin trabajo manual del founder |
| Moat | **Central** — descubrimiento + comparación + aprendizaje es difícil de replicar y mejora con los datos de uso (RFC-0008) |
| Costo/complejidad | Alto — scraping resiliente de OTAs, matching por características, pipeline de aprendizaje; sobredimensionado para Fase 1 |
| Riesgo multi-tenancy | Alto si se hace mal: el scraper corre con service_role → DEBE filtrar `property_id` explícito, nunca confiar solo en RLS (DOC-0031 §3, clase de bug ya identificada en `competitor_scraper_results`) |

### Option C: Semi-automático (scraping asistido, dueño valida cada match)
| Dimensión | Evaluación |
|---|---|
| Escalabilidad | Media — reduce el trabajo manual pero no lo elimina |
| Rol | Puede ser un **paso intermedio** de rollout de la Option B, no un destino |

## Trade-off Analysis

La Option A es la correcta HOY (Fase 1, un cliente ancla) pero es un techo de
crecimiento, no una estrategia. La Option B es el mecanismo que convierte la
"validación de pricing contra el mercado real" de un servicio manual en un
producto escalable, y es donde reside el moat (RFC-0008: el Competitor Scout
alimenta el Growth Brain, que mejora con cada decisión del dueño). Su costo y su
riesgo de multi-tenancy la hacen inapropiada para Fase 1 (ADR-010). La Option C
es un rollout intermedio posible, no un destino. Se elige B, **diferida a Fase 2**,
y se registra ahora para que las decisiones de Fase 1 (esquema de
`competitor_scraper_results`, captura de `bdvl_decisions`) no cierren la puerta a
implementarla.

## Consequences

**Facilita (cuando se implemente):**
- Onboarding de clientes nuevos sin trabajo manual del founder → escala real
- Moat defendible: descubrimiento + comparación + aprendizaje autónomo (RFC-0008)
- La transparencia de Fase 1 (link verificable por competidor) se mantiene: el
  scraping automático debe seguir exponiendo la fuente, no volverse una caja negra

**Dificulta:**
- Scraping de OTAs es frágil (anti-bot, cambios de layout) y legalmente sensible
- Matching por características e "ajuste por diferencias" pueden errar → el humano
  debe seguir en el loop (BDVL) hasta que el Decision Graph gane confianza
- Riesgo de fuga multi-tenant si el worker con service_role no filtra por
  `property_id` (DOC-0031 §3) — la clase de bug ya ocurrida

**A revisar:**
- Qué OTAs primero (Booking como ancla) y política de scraping (frecuencia, caché)
- Modelo de datos del Decision Graph (¿extender `bdvl_decisions`? ¿tabla nueva?)
- Cómo se calcula el "ajuste por diferencias" y cómo se explica al dueño (ARCH-0028)
- Relación con ADR-014: el Scout da el precio de competidores; el techo externo
  (Brasil/macro) sigue siendo un guardrail de demanda separado

## Action Items
1. [ ] NO implementar en Fase 1 — este ADR es borrador (Proposed)
2. [ ] Fase 1 (ya en curso): mantener carga manual transparente; asegurar que
   `bdvl_decisions` capture aceptación/rechazo del dueño (materia prima del Decision Graph)
3. [ ] Fase 2: diseñar el Competitor Scout sobre RFC-0008 (scraping OTAs +
   matching por características + ajuste + Decision Graph)
4. [ ] Fase 2: garantizar filtro `property_id` explícito en todo query del worker
   con service_role (DOC-0031 §3)
5. [ ] Fase 2: definir si hay un rollout intermedio semi-automático (Option C)

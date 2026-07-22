# ADR-010-E: Enmienda — Mandato del Panel para Alcance Fase 1 y Secuencia F2→F3

**Status:** Accepted
**Date:** 2026-07-21
**Amends:** ADR-010 (Phase 1 Scope Reduction)
**Deciders:** Denis Gueler (founder), panel de arquitectura (4 loops, ATLAS_PLAN_MAESTRO_SAAS.md)

<!-- NOTA F0 (2026-07-21): Este documento se registra bajo ADR-010-E, NO bajo "ADR-012" como
pedía la tarea F0 original. Motivo doble:
(1) ADR-012 ya existe y está Accepted (ADR-012_AGENTIC_DISCOVERABILITY_PHASE2_PRIORITY.md,
"Discoverability Agéntica (MCP) como Prioridad Central de Fase 2") — reclamar el mismo ID para
un documento distinto es la colisión que F0 existe para prevenir.
(2) El pedido describía el contenido como "la estrategia operativa desde hace semanas y nunca
se documentó" — esa premisa es FALSA: ADR-010 (Accepted, 2026-07-03) ya documenta exactamente
esta reducción de alcance a Revenue Agent de Fase 1, con la secuencia Fase 2→Fase 3 explícita
en su Context ("intentó implementar un componente de Fase 3 ... sin las fundaciones de Fase 2")
y la preservación de NeuralCore como referencia de largo plazo en su Decision. Redactar un ADR
nuevo con ese contenido habría creado un documento ~90% redundante con uno ya vigente.
Lo genuinamente NUEVO respecto de ADR-010 es el mandato del panel (4 loops, no solo el
founder) y la razón de fondo — "fundador solo no-técnico" — que ADR-010 no articula. Eso es lo
que este documento agrega, como enmienda, siguiendo el mismo patrón que ADR-010-B/C/D
(pisos agregados sin reabrir el ADR original). No se tocó ADR-010 (los ADR son inmutables).
Pendiente: corregir en ATLAS_PLAN_MAESTRO_SAAS.md línea 248 ("ADR de reducción de alcance
Fase-1 ... hoy sin documentar") — la premisa "sin documentar" es incorrecta, ya está en
ADR-010; la referencia correcta tras F0 es ADR-010-E. Fuera de scope de F0 tocar ese archivo. -->

## Context

ADR-010 (2026-07-03) ya redujo el alcance de Phase 1 a Revenue Agent únicamente, con la misma
secuencia de fondo (Fase 3 no se construye sin las fundaciones de Fase 2) y la misma decisión de
preservar NeuralCore como visión de largo plazo sin descartarla. Lo que ADR-010 no registra es
el **mandato explícito** detrás de esa reducción: no es una preferencia del founder, es el
veredicto de un panel de arquitectura corrido en 4 loops (ATLAS_PLAN_MAESTRO_SAAS.md §0, §12),
y la razón estructural que lo sostiene — Denis es un fundador solo, no técnico — no está
articulada en ADR-010 como principio operativo continuo (solo aparece implícita en ADR-003-A
respecto de la fricción de WhatsApp Business API).

Sin ese mandato registrado, cualquier futura propuesta de "adelantar" una pieza de Fase 3
(ej. una feature de pricing más sofisticada) carece de un ADR que citar para rechazarla por
secuenciación, más allá de la lectura implícita de ADR-010.

## Decision

Se confirma y refuerza ADR-010, sin modificarlo: el alcance operativo vigente es **Fase 1
(Revenue Copilot + operación conversacional)**, y esto es un veredicto de panel, no una
decisión informal del founder. La visión completa NeuralCore (10 módulos) permanece como
**REFERENCIA de largo plazo**, tal como ya establecía ADR-010 — este documento no cambia eso,
lo reafirma con el mandato explícito detrás.

Se hace explícita, como principio operativo continuo (no solo como diagnóstico puntual de
ADR-010), la secuencia: **Fase 2 (fundaciones — AI Orchestrator, Context Builder, Decision
Graph) precede a cualquier expansión de Fase 3**. Toda propuesta de expandir Fase 3 sin que
Fase 2 esté resuelta se rechaza por este principio, sin necesidad de re-discutirlo caso a caso.

Razón de fondo (nueva respecto de ADR-010): **fundador solo, no técnico**. Cada componente
nuevo debe justificar por qué no es, en cambio, una tabla, un test o un prompt — anti-sobre-
ingeniería estructural, no una limitación temporal a superar apenas haya tiempo.

## Consequences

**Facilita:**
- Un ADR citable para rechazar expansión prematura de Fase 3, sin reabrir la discusión de
  ADR-010 cada vez.
- Deja registrado que la reducción de alcance es un consenso de panel, no una decisión
  reversible por preferencia individual del día.

**Dificulta:**
- Ninguna nueva respecto de ADR-010 — este documento no agrega restricciones, formaliza las
  que ya regían de facto.

**A revisar:**
- Criterio de salida Fase 2→Fase 3 sigue siendo el de ADR-010 (§"A revisar": "qué métrica de
  LindaBay dispara la inversión en Context Builder/Property Brain"), aún sin cerrar.

## Action Items
1. [ ] Corregir en `ATLAS_PLAN_MAESTRO_SAAS.md` línea 248 la premisa "hoy sin documentar" y
   apuntar la referencia a ADR-010 + ADR-010-E.
2. [ ] Al proponer cualquier feature de Fase 3, citar este documento (o ADR-010) en el brief
   antes de estimar — no después.

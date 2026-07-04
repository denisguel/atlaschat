# ADR-010-B: Piso de Contexto Mínimo para Phase 1

**Status:** Accepted
**Date:** 2026-07-03
**Amends:** ADR-010 (Phase 1 Scope Reduction)
**Deciders:** Denis Gueler (founder)

## Context

ADR-010 excluye Context Builder y Property Brain de Phase 1. Sin datos de contexto reales, el Revenue Agent repite el error diagnosticado en el root cause original: recomendaciones genéricas. Se necesita un mecanismo de contexto sin construir la infraestructura de memoria semántica completa (RFC-0003).

## Decision

El Revenue Agent inyecta directo en cada prompt, vía queries SQL simples (no Context Builder, no embeddings, no Brain):

| Dato | Fuente | Mecanismo |
|---|---|---|
| Atributos fijos de la propiedad (unidades, ubicación, temporada) | Tabla `properties` en Supabase | Query directa, sin caché semántica |
| Precios de competidores | Output del scraper existente | Query directa a tabla de resultados del scraper |
| Historial de ocupación/pricing reciente | Tabla `reservations`/`pricing_history` | Query directa, ventana de 30-90 días |

**Excluido explícitamente:** embeddings, búsqueda semántica, memoria de conversaciones pasadas, Guest/Growth Brain. Esto se difiere a Phase 2 (RFC-0003) cuando exista volumen de datos que lo justifique.

## Consequences

**Facilita:** recomendaciones específicas de LindaBay sin construir Context Builder — resuelve la causa raíz del fallo original con una función, no una arquitectura.

**Dificulta:** el Revenue Agent no "aprende" de interacciones pasadas todavía — es contexto estático por consulta, no memoria acumulativa. Aceptado como límite conocido de Phase 1.

## Action Items
1. [ ] Definir las 3 queries SQL exactas (propiedad, competidores, histórico) como funciones reutilizables
2. [ ] Documentar el prompt template del Revenue Agent con estos 3 bloques de contexto inyectados

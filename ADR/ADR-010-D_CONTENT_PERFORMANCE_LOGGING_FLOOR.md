# ADR-010-D: Piso de Logging de Performance de Contenido para Fase 1

**Status:** Accepted
**Date:** 2026-07-03
**Amends:** ADR-010 (Phase 1 Scope Reduction)

## Context

El moat de "aprendizaje de contenido" (qué hook/formato/horario genera reservas) requiere acumulación de datos desde el día 1 — no se puede recuperar retroactivamente. Es la misma categoría que el Decision Graph (ARCH-0013): moat por tiempo+datos, no por sofisticación de IA. Diferirlo a Fase 2 significa perder meses de datos irrecuperables.

## Decision

Se agrega a Fase 1 (barato, determinístico, sin Content/Viral Agent):

- Logging de metadata por publicación: hook, formato, canal, horario, temporada (tabla `content_assets`)
- Atribución de reservas por publicación vía UTM (tabla `content_performance`)
- Sin generación automática avanzada (Replicate/Runway), sin análisis viral cross-cuenta — eso sigue en Fase 2+

**Sigue excluido:** Viral Analyzer Agent, generación de imágenes/video con IA paga, aprendizaje cross-cliente automatizado (requiere volumen de clientes que Fase 1 no tiene).

## Consequences

**Facilita:** el moat de datos empieza a acumularse desde el primer post publicado, no cuando "full intelligence" esté lista — evita perder meses de historial.

**Dificulta:** overhead mínimo de instrumentación (2 tablas, tracking UTM) antes de publicar el primer contenido.

## Action Items
1. [ ] Tabla `content_assets` (hook, formato, canal, horario, temporada, property_id)
2. [ ] Tabla `content_performance` (content_asset_id, reservas_atribuidas, revenue_atribuido)
3. [ ] UTM tracking en cada link publicado

# ADR-010-C: Piso de Seguridad y Costo Liviano para Phase 1

**Status:** Accepted
**Date:** 2026-07-03
**Amends:** ADR-010 (Phase 1 Scope Reduction)
**Deciders:** Denis Gueler (founder), Panel de Expertos (revisión cruzada con autopsia externa)

## Context

Un análisis externo ("autopsia ATLAS") diagnosticó 6 modos de fallo hipotéticos si se construye la visión completa sin disciplina de ejecución: muro de latencia, token burn, sobre-ingeniería, fricción de BDVL, inconsistencia de datos, límites de WhatsApp-first.

Verificación cruzada contra el corpus real (`project_knowledge_search`) confirma: **los 6 modos de fallo ya tienen solución especificada** en ARCH-0021, ARCH-0022, ARCH-0023, ARCH-0025, ARCH-0027, ARCH-0018 §20. No se requiere arquitectura nueva.

Sin embargo, **ADR-010 excluyó de Phase 1 dos categorías distintas bajo la misma etiqueta ("sin IA sofisticada")**:
1. Memoria semántica pesada (Brains, Context Builder multi-fuente, Multi-Agent) — correctamente excluida
2. Disciplina determinística barata (BDVL-como-reglas, Execution Levels, budgets) — excluida por error, es precisamente lo que previene los fallos de la autopsia

## Decision

Se formaliza un **piso mínimo de seguridad y costo** para Phase 1, distinto del piso de contexto (ADR-010-B, si fue generado en Claude Code — verificar y reconciliar):

### Incluido en Phase 1 (barato, determinístico, ya especificado)

| Componente | Fuente | Por qué es barato |
|---|---|---|
| BDVL como policy engine (tabla de reglas, no servicio) | ARCH-0022 §10-14, §25 | Clasificación LOW/MEDIUM/HIGH/CRITICAL es lógica if/else sobre datos ya existentes en Supabase — no requiere IA |
| Execution Levels con Fast Path L0 | ARCH-0021 §17 | Reglas/templates para consultas rutinarias — cero costo de IA |
| Token/Latency budgets por nivel | ARCH-0021 §20-21 | Límites duros de configuración, no lógica nueva |
| AI Budget + Graceful Degradation | ARCH-0027 §20-21 | Resuelve la decisión pendiente de proveedor (Claude vs gratuito) automáticamente — degrada en cascada L4→L0 si se excede el cap, en vez de decisión binaria manual |
| Organización de código Monolito Modular | ARCH-0025 §7-9 | Es organización de carpetas, no infraestructura — costo cero, paga dividendos en Phase 2 |
| Dashboard escalation para tareas visuales | ARCH-0023 §10 | Un link, no una feature nueva |

### Sigue excluido de Phase 1 (correcto en ADR-010, sin cambios)

- Property/Guest/Growth Brain (memoria vectorial, embeddings)
- Multi-Agent coordination (Planner, múltiples agentes colaborando)
- Context Builder de múltiples fuentes (se usa el "piso de contexto mínimo" de ADR-010-B en su lugar)

## Resolución de la decisión pendiente (Claude vs modelo gratuito)

**Ya no es una decisión binaria a tomar ahora.** Se implementa el AI Budget de ARCH-0027 §20-21 con cap inicial de **$20/mes**. El sistema usa Claude por defecto en L2-L4 (ADR-011); si el budget se agota, degrada automáticamente L4→L3→L2→L1 hacia modelo gratuito, sin intervención manual. Se ajusta el cap con datos reales tras 2-4 semanas de uso en LindaBay.

## Consequences

**Facilita:**
- Cierra exactamente los 6 modos de fallo de la autopsia sin agregar peso arquitectónico a Phase 1
- Resuelve la pregunta de proveedor de IA con mecanismo ya especificado, no debate nuevo
- Código organizado desde el día 1 para extensión a Phase 2 sin reescritura

**Dificulta:**
- Ligero overhead de implementación (tabla de reglas BDVL, config de budget) antes de la primera línea del Revenue Agent — aceptado, es el mismo principio que BDVL logging en ADR-010

**A revisar:**
- Verificar si ADR-010-B (piso de contexto mínimo) fue generado en la sesión de Claude Code — si no, generarlo para completar el set de amendments de ADR-010

## Action Items
1. [ ] Implementar tabla de reglas BDVL (policy engine) — LOW/MEDIUM/HIGH/CRITICAL sobre acciones del Revenue Agent
2. [ ] Configurar AI Budget cap ($20/mes inicial) con Graceful Degradation
3. [ ] Organizar repo de código siguiendo Modular Monolith (carpetas lógicas separadas: `revenue-agent/`, `bdvl/`, `execution-levels/` — aunque hoy solo un módulo esté activo)
4. [ ] Confirmar estado de ADR-010-B en el repo local

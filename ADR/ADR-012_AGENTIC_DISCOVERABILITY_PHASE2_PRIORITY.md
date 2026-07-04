# ADR-012: Discoverability Agéntica (MCP) como Prioridad Central de Fase 2

**Status:** Accepted
**Date:** 2026-07-03
**Relaciona:** ADR-007 (MCP-Ready Architecture), ARCH-0019 (Product Roadmap)
**No amends:** ADR-010 (Fase 1 sin cambios)

## Context

Verificación de mercado (jul-2026): tráfico referido por IA a retail +805% interanual, compradores vía IA 38% más propensos a comprar, MCP consolidado como capa base de todos los protocolos de commerce (ACP, UCP, A2A). Operadores independientes de 1-5 unidades no tienen CRS empresarial ni estrategia MCP — brecha sin resolver en el segmento.

ADR-007 ya estableció arquitectura MCP-ready como decisión técnica. Este ADR la eleva a **prioridad estratégica central de Fase 2**, no un ítem técnico más.

## Decision

Fase 2 prioriza, por encima de sofisticación de ML de pricing:

1. Exponer cada propiedad como servidor MCP/UCP descubrible por agentes de IA (ChatGPT, Claude, Gemini)
2. Checkout permanece merchant-controlled (WhatsApp/MercadoPago) — la IA maneja discovery+intent, no el pago (alineado con retiro del checkout nativo de OpenAI por confianza del consumidor)
3. Capas 2-4 del moat (Content Performance, Decision Graph, Densidad Regional — ATLAS_MOAT_AGENTICO_TESIS.md) se acumulan en paralelo desde Fase 1, no esperan a Fase 2

**Fase 1 no se modifica.** Esto es reordenamiento de prioridad de Fase 2, no expansión de scope actual.

## Consequences

**Facilita:** posicionamiento en la capa de distribución que Booking/Airbnb no controla, antes de que el segmento de 1-5 unidades tenga alternativa.

**Dificulta:** protocolos en flujo (ACP vs UCP) — mitigado construyendo MCP-native primero, adaptadores después.

## Action Items
1. [ ] Actualizar ARCH-0019 §Roadmap: Fase 2 lista MCP-discoverability como ítem #1, ML pricing baja de prioridad
2. [ ] Sin acción en Fase 1 — este ADR es guía para cuando se abra Fase 2

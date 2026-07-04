# ADR-003-A: Enmienda — Telegram como Canal Interino de Phase 1

**Status:** Accepted
**Date:** 2026-07-03
**Amends:** ADR-003 (WhatsApp as Primary Interface)
**Deciders:** Denis Gueler (founder)

## Context

ADR-003 estableció WhatsApp como interfaz primaria del producto (principio "WhatsApp First" en AES-00). El sistema desplegado (`atlas-mvp`) usa **Telegram**, no WhatsApp. Esta divergencia existe hace tiempo pero nunca fue documentada como decisión — quedó como deuda técnica silenciosa, detectada en auditoría de documentación.

Razón práctica de la divergencia (no documentada previamente, reconstruida por contexto): la API de WhatsApp Business requiere aprobación de Meta, número verificado y, en muchos casos, un Business Solution Provider — fricción de setup incompatible con la velocidad de iteración de un fundador solo, no técnico, validando un MVP.

## Decision

Se formaliza Telegram como **canal interino de Phase 1** (ADR-010). WhatsApp permanece como **canal objetivo estratégico** para Phase 2+, sin cambios a la tesis de "WhatsApp First" de cara al mercado LATAM (donde WhatsApp domina la comunicación, no Telegram).

La capa de integración debe construirse de forma que el canal sea **intercambiable**, no hardcodeado — esto ya está parcialmente alineado con ADR-007 (MCP-Ready Architecture) y ADR-008 (AI Provider Abstraction): el mismo patrón de abstracción se aplica al canal de mensajería.

## Options Considered

### Option A: Migrar a WhatsApp inmediatamente
| Dimensión | Evaluación |
|---|---|
| Alineación con tesis de mercado | Alta |
| Fricción de setup | Alta — aprobación Meta, verificación de negocio |
| Velocidad de Phase 1 | Se retrasa semanas/meses |

### Option B: Formalizar Telegram como interino, abstraer el canal (elegida)
| Dimensión | Evaluación |
|---|---|
| Alineación con tesis de mercado | Media en el corto plazo, alta en destino |
| Fricción de setup | Ninguna — ya operativo |
| Velocidad de Phase 1 | Sin impacto |
| Deuda técnica | Baja, si la abstracción de canal se implementa correctamente desde ahora |

### Option C: Mantener Telegram permanentemente, descartar tesis WhatsApp
| Dimensión | Evaluación |
|---|---|
| Alineación con tesis de mercado | Baja — Telegram no es el canal dominante en el segmento LATAM objetivo (dueños de alquileres temporarios, huéspedes) |
| Riesgo de negocio | Alto — contradice AES-00 |

## Trade-off Analysis

Option B preserva la tesis de mercado (crítica para adopción real en LATAM) sin bloquear el progreso de Phase 1 por fricción operativa de WhatsApp Business API. El costo es disciplina de ingeniería: la capa de mensajería debe tratarse como un proveedor intercambiable (mismo patrón que ADR-008 para IA), no como Telegram-específico.

## Consequences

**Facilita:**
- Phase 1 avanza sin bloqueo de aprobación de terceros (Meta)
- Migración futura a WhatsApp no requiere reescritura si la abstracción se respeta desde ahora

**Dificulta:**
- Requiere disciplina adicional: no hardcodear llamadas a la API de Telegram directamente en la lógica de negocio del Revenue Agent

**A revisar:**
- Criterio de disparo para migrar a WhatsApp (¿número de propiedades? ¿feedback explícito de usuarios sobre preferencia de canal?)

## Action Items
1. [ ] Verificar que la integración actual de Telegram esté detrás de una interfaz abstracta de "canal de mensajería", no acoplada directamente
2. [ ] Definir criterio de disparo para iniciar el proceso de aprobación de WhatsApp Business API
3. [ ] Actualizar AES-00 Project DNA para reflejar "WhatsApp First (target) / Telegram (interino Phase 1)" en vez de dejarlo ambiguo

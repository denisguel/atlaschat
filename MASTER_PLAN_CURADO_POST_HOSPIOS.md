# ATLAS — Master Plan Curado (post-análisis HospiOS)

**Fecha:** 2026-07-03 | **Estado:** Consensuado por panel, listo para ejecución

## 1. Veredicto sobre el documento HospiOS

Fue generado independientemente (Kimi.ai) sobre la misma tesis de negocio que ATLAS/LindaBay. Contiene ideas valiosas y un error crítico:

**Error crítico — Sección 23 (Agent Swarm):** CEO Agent + 3 departamentos + 15 sub-agentes especializados con orquestación compleja. Es la reconstrucción exacta del root cause ya diagnosticado (Phase 3 sin Phase 2), ahora aplicado a una propiedad de 2 unidades. **Rechazado.** No se construye en ningún punto de Fase 0-1. Se reevalúa solo si el volumen real de múltiples propiedades lo justifica con datos, no antes.

**Fantasía de escala prematura:** proyecciones de \$4.8-15M ARR a año 3 no informan ninguna decisión de esta fase. Ignoradas para efectos de planificación.

**Valioso, incorporado con corrección:**
- Disciplina de staging (HTML-to-Image gratis antes de Replicate/Runway pagos) — coincide con ARCH-0025
- Cuotas argentinas vía MercadoPago — crítico para conversión local, bajo costo
- Insight de canal de venta: 128 propietarios del mismo complejo que LindaBay
- Corrección de modelo de costos: WhatsApp Business API vía BSP (no gratis, requiere aprobación) — confirma ADR-003-A

**Corrección aplicada:** HospiOS asume Apify (\$49/mes) para scraping de competidores. ATLAS ya tiene un scraper propio funcionando — reutilizarlo evita ese costo. Impacto: preserva ~24 puntos de margen en la cohorte founding (66% vs 42% a 5 clientes).

## 2. Roadmap curado (versión propia, no copia de HospiOS)

| Fase | Duración | Scope | Costo mensual |
|---|---|---|---|
| **Fase 0 — Validación** | Meses 1-2 | LindaBay (2u). Revenue Agent + piso de contexto (ADR-010-B) + BDVL-reglas (ADR-010-C) + Telegram + CRUD básico | $2-8 |
| **Fase 1 — Founding Partners** | Meses 3-5 | 5-10 clientes (1-5u c/u). MVP Must-Have completo: reservas, WhatsApp/Telegram, MercadoPago+cuotas, agente FAQ, Revenue Agent, dashboard, contenido HTML-to-image, scraper propio | $68-121 (ver tabla) |
| **Fase 2 — Intelligence Layer** | Meses 6-12 | Dynamic pricing ML, content learning loop, review mining, Growth Brain básico. Multi-agente SOLO si datos reales lo justifican | A definir con telemetría |
| **Fase 3+ — Marketplace/Fintech/Data** | Año 2+ | Diferido sin cambios — requiere escala real y probablemente capital externo | N/A |

## 3. Modelo financiero corregido — Fase 1

| | 5 clientes | 10 clientes |
|---|---|---|
| Revenue | $200/mes | $400/mes |
| Infra (Supabase Pro + Vercel Pro, **sin Apify**) | $45/mes | $45/mes |
| Tokens IA | $15-30/mes | $30-60/mes |
| WhatsApp/Telegram | $0 (Telegram) | $0 (Telegram) |
| Payment processing (~4%) | $8/mes | $16/mes |
| **Profit** | **$117-132/mes (58-66%)** | **$279-309/mes (70-77%)** |

Sin cambios respecto al modelo previo — la corrección del scraper evita que este número se deteriore.

## 4. Estrategia de adquisición (nuevo, del análisis HospiOS)

1. Validar 30-60 días en LindaBay (Fase 0)
2. Si funciona: ofrecer a los otros propietarios del mismo complejo antes que a mercado frío — menor CAC, referencia directa verificable
3. 5-10 founding partners desde ese pool antes de salir a mercado general

## 5. Pendiente de tu número real

El objetivo de utilidad mensual para "enfocarme exclusivamente en esto" sigue sin definir — es tu decisión personal, la fórmula y tabla de referencia quedaron en `BUSINESS_CASE_AND_FINANCIAL_MODEL.md`.

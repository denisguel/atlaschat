# ATLAS — Business Case y Modelo Financiero

**Fecha:** 2026-07-03
**Estado:** Draft — pendiente de validar con telemetría real (ARCH-0027 §22)

## 1. Resolución de scope para monetización

"Todas las funcionalidades" para cobrar a clientes = **MVP Must-Have de `02_MVP_SCOPE.md` §4**, no la visión NeuralCore completa:

**Incluido (vendible):**
- Propiedades, huéspedes, reservas (CRUD)
- Inbox WhatsApp/Telegram
- Guest/Property Brain **básico** (tabla estructurada Supabase, no memoria vectorial/semántica)
- Agente conversacional (FAQ, disponibilidad, precios, políticas)
- Revenue Agent (ADR-010 + ADR-010-B contexto mínimo + ADR-010-C BDVL-reglas)
- Dashboard básico
- Automatizaciones core (n8n)

**Excluido (sigue diferido, sin cambios a ADR-010):**
- Multi-Agent orchestration completo
- Growth Intelligence / Direct Booking Engine
- Memoria semántica vectorial (RFC-0003)
- Decision Graph como infraestructura de moat

## 2. Fase 0 — Validación (Meses 1-2, LindaBay, 2 unidades)

| Ítem | Costo mensual |
|---|---|
| Supabase Free | $0 |
| Vercel Hobby (uso propio, no comercial) | $0 |
| n8n (Oracle Cloud Always Free) | $0 |
| Tokens IA (Claude L2-L4 + gratuito L1) | $2-8 |
| **Total** | **$2-8/mes** |

Criterio de salida a Fase 1: Revenue Agent genera recomendaciones específicas (no genéricas) validadas manualmente por 2-4 semanas en LindaBay.

## 3. Fase 1 — Founding Partners (5-10 clientes, 1-5 unidades c/u, $40/mes)

| Métrica | 5 clientes | 10 clientes |
|---|---|---|
| Revenue | $200/mes | $400/mes |
| Infra (Supabase Pro + Vercel Pro, compartido) | $45/mes | $45/mes |
| Tokens IA (15-30 unidades total) | $15-30/mes | $30-60/mes |
| Payment processing (~4%) | $8/mes | $16/mes |
| **Costo total** | **$68-83/mes** | **$91-121/mes** |
| **Profit** | **$117-132/mes** | **$279-309/mes** |
| Margen | 58-66% | 70-77% |

## 4. Framework de objetivo de utilidad mensual

No hay un número "correcto" universal — depende de costo de vida personal, otros ingresos, tolerancia al riesgo. Fórmula:

```
Clientes necesarios = Objetivo de utilidad (USD) / ~$28 (profit promedio por cliente founding-tier)
```

| Objetivo (USD/mes) | Clientes founding-tier | Equivalente ARS (blue $1.500) |
|---|---|---|
| $300 | ~11 | ~$450.000 |
| $600 | ~21 | ~$900.000 |
| $1.000 | ~36 | ~$1.500.000 |
| $1.500 | ~54 | ~$2.250.000 |
| $2.500 | ~89 | ~$3.750.000 |

Más allá de los primeros 10 founding partners, migrar a pricing tiered/por-unidad (discutido previamente) mejora el profit-por-cliente — esta tabla es un techo conservador asumiendo $40 flat indefinidamente.

## 5. Estructura financiera — Argentina

- **Precio en USD**: cobertura natural, ya que el costo variable principal (tokens Claude) se factura en USD.
- **Monotributo Categoría A** (tope ~$12M ARS/año, ARCA vigente desde jul-2026) cubre holgadamente la facturación proyectada de la cohorte founding. Confirmar con contador antes de facturar.
- **Payment processor**: Mercado Pago (dólar-linked) o Stripe para cobro en USD — decisión de implementación, no arquitectónica.

## 6. Riesgos financieros no cuantificados (a monitorear)

- Estimaciones de tokens son modelo, no telemetría real — validar en semana 1-2 de Fase 1
- Costo de adquisición de clientes (CAC) no está modelado — este documento cubre unit economics, no funnel de ventas
- Churn de founding partners no proyectado — requiere datos reales tras 60-90 días de uso

## 7. Próximos pasos

1. Ejecutar Fase 0 con instrumentación de costo real desde el día 1 (ARCH-0027 §22)
2. Validar supuestos de este modelo con datos de LindaBay antes de fijar precio final para mercado general
3. Definir estructura de tiering post-founding (10+ clientes) con datos reales, no estimaciones

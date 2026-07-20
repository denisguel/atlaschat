# SPEC — Modelo Proactivo de Precios (el CÓMO operativo de RFC-0008)

**Fecha:** 2026-07-20
**Estado:** Canonical — corrige un error de construcción fundamental
**Por qué existe:** RFC-0008 especifica claramente un modelo PROACTIVO ("Opportunity detection operates continuously rather than only when the owner requests an analysis"). El sistema se construyó REACTIVO (un chatbot que responde consultas). Este documento traduce la visión de RFC-0008 en tablas, procesos y disparadores concretos — el CÓMO que faltaba.

**El error a corregir:** ATLAS NO es un bot que calcula un precio cuando le preguntás por Telegram. Es un sistema que MANTIENE una tabla de precios por unidad/fecha y te AVISA cuando conviene ajustarla.

---

## 1. El modelo correcto (paradigma)

### INCORRECTO (lo construido)
```
Usuario pregunta por Telegram → sistema calcula un precio al vuelo → responde → se pierde
```

### CORRECTO (RFC-0008)
```
1. El sistema MANTIENE una tabla de precios: cada unidad × cada fecha = tarifa definida
2. Un proceso corre en BACKGROUND, continuamente, vigilando señales (competencia, ocupación, pace, techo)
3. Cuando una señal cambia y afecta un precio → el sistema DETECTA la oportunidad
4. AVISA al propietario proactivamente: "conviene ajustar estas fechas por esto"
5. El propietario aprueba/rechaza → la tabla se actualiza
6. Telegram es el canal de AVISOS Y APROBACIÓN, no de consultas de precio
```

## 2. La tabla de precios (el corazón, persistente)

**Tabla `price_calendar` — la fuente de verdad de los precios vigentes:**
```sql
price_calendar (
  id uuid PK,
  unit_id uuid FK -> units,
  stay_date date,              -- cada fecha del horizonte tiene su fila
  price_usd numeric,           -- la tarifa VIGENTE para esa unidad esa fecha
  pricing_model text,          -- 'per_night' | 'package' (heredado de la temporada)
  day_type text,               -- 'weekday' | 'weekend' (para per_night)
  source text,                 -- 'seasonal_rule' (generado de temporada) | 'manual' (editado por dueño) | 'ai_adjusted' (ajuste aprobado)
  season_id uuid FK,           -- qué temporada le dio origen
  last_reviewed_at timestamptz,-- cuándo el sistema evaluó por última vez si sigue óptimo
  updated_at timestamptz,
  UNIQUE(unit_id, stay_date)
)
```

**Cómo se GENERA inicialmente:**
- A partir de las 13 temporadas (`rate_seasons` + `rate_season_dates` + `unit_rates`), el sistema PUEBLA `price_calendar` para todo el horizonte (ej. próximos 12 meses)
- Cada fecha recibe su tarifa según: qué temporada la cubre, qué modelo (per_night/package), qué day_type (L-J/V-D)
- Esto es un proceso determinístico que corre una vez y se regenera si cambian las temporadas
- **Resultado: una grilla completa de precios que el propietario puede VER y EDITAR** (es su calendario de tarifas)

## 3. El motor de vigilancia (background, continuo — RFC-0008 "continuously scans")

**Un proceso programado (cron) que corre periódicamente y NO depende de consultas del usuario:**

Para cada unidad × cada fecha del horizonte:
1. Trae las señales actuales: competencia real por fecha (scraper), ocupación, pace, techo
2. Compara el precio VIGENTE en `price_calendar` contra lo que las señales sugieren
3. Si hay una divergencia significativa → registra una OPORTUNIDAD

**Tabla `pricing_opportunities` (lo que el sistema detecta):**
```sql
pricing_opportunities (
  id uuid PK,
  unit_id uuid FK,
  stay_date date,              -- o rango
  current_price_usd numeric,   -- lo que está en price_calendar hoy
  suggested_price_usd numeric, -- lo que las señales sugieren
  reason text,                 -- "competencia subió 15%", "ocupación 90% a 60 días", "gap detectado"
  signals jsonb,               -- qué señales dispararon (competencia/ocupación/pace/techo)
  confidence numeric,
  status text,                 -- 'pending' | 'approved' | 'rejected' | 'expired'
  detected_at timestamptz,
  resolved_at timestamptz,
  resolved_by uuid
)
```

**Disparadores de oportunidad (RFC-0008 "Price adjustments may be triggered by"):**
- Competencia comparable cambió (subió/bajó) para esa fecha
- Ocupación alta con anticipación (subir) o baja con fecha cerca (bajar)
- Booking pace atrasado/adelantado vs. histórico
- Gap night detectado
- Techo cambió (nuevo dato de PXSOL/destino alternativo)
- El precio vigente quedó por encima del techo

**LOS 20 GATILLOS (ver manual_revenue_pricing.md — la inteligencia concreta del motor):**

El motor de vigilancia NO improvisa qué buscar ni cuánto ajustar. Usa los 20 gatillos del manual profesional, cada uno con su peso en RevPAR, umbral de disparo y % de ajuste. Los principales, mapeados a señales que el sistema puede calcular:

| # | Gatillo | Señal que lo dispara | Ajuste | Dato disponible |
|---|---|---|---|---|
| 1 | Estacionalidad (peso 35%) | temporada de la fecha | +50% a +120% | ✅ rate_seasons |
| 2 | Pick-up anómalo (15%) | OTB ≥65% a 60 días | +15% a +30% | ⏳ requiere reservas/pace |
| 3 | Eventos/feriados (10%) | evento detectado | +25% a +45% | señal externa (futuro) |
| 8 | Penalización 1 noche (5%) | reserva de 1 noche en alta demanda | +20% a +35% | ✅ min_nights |
| 9 | Competencia sube (5%) | 80% del compset subió | +10% a +18% | ✅ scraper (cuando ande) |
| 11 | Temporada baja (30%) | temporada baja | -30% a -50% | ✅ rate_seasons |
| 12 | Last minute (15%) | vacío a 48-72h del finde | -15% a -30% | ⏳ requiere reservas |
| 13 | Long stay (12%) | reserva 7/14/30 noches | -10% a -25% | ✅ package/min_nights |
| 14 | Early bird (10%) | venta 6-9 meses antes | -15% a -25% | ✅ lead time |
| 18 | Venta directa (5%) | huésped directo/recurrente | -10% a -15% | ✅ source directo |
| 20 | Bache mid-week (3%) | L-M-X vacíos sistemáticos | -20% a -35% | ✅ day_type weekday |

Los gatillos marcados ✅ se pueden implementar YA (el dato existe). Los ⏳ requieren que se acumulen reservas (pace/ocupación). Los de señal externa (eventos) son futuro.

**Regla de aplicación (del manual, "Resumen de comportamiento"):**
- Alta presión (pick-up rápido / eventos / alta temp): +15-30% base, restringir min_nights, desactivar promos
- Contracción (baja ocupación / crisis): activar no-reembolsable -15%, bajar mid-week, last-minute en directo

**Net-RevPAR (manual Paso 4) — crítico para el objetivo de maximizar ingresos:**
El precio óptimo no es el bruto sino el NETO por canal. Tarifa externa = tarifa neta deseada / (1 - comisión del canal). Ej: neta $150 + Booking 18% → listar a $182.90. El sistema debe considerar la comisión del canal al comparar competencia (un precio de Booking incluye su comisión) y al sugerir tarifas por canal.

## 4. Alertas proactivas (el sistema AVISA, no espera)

Cuando hay `pricing_opportunities` con status='pending' de confianza suficiente:
- El sistema NOTIFICA al propietario por Telegram (o el canal configurado), SIN que pregunte
- Mensaje: "Detecté una oportunidad: [unidad] para [fechas]. Precio actual $X, sugiero $Y porque [razón]. ¿Aprobás? /aprobar [id]"
- El propietario aprueba → `price_calendar` se actualiza (source='ai_adjusted'), la oportunidad pasa a 'approved'
- Rechaza → queda registrado (aprende de qué rechaza), la oportunidad pasa a 'rejected'

## 5. Telegram — cambia de rol

| Rol viejo (incorrecto) | Rol nuevo (correcto) |
|---|---|
| "consultá un precio" → calcula al vuelo | Recibe ALERTAS de oportunidades detectadas |
| Reactivo: solo actúa si preguntás | Proactivo: te avisa cuando algo cambió |
| Respuesta efímera | Aprobás/rechazás → impacta la tabla persistente |

**Consultas puntuales siguen siendo posibles** ("¿cómo está enero?"), pero NO son el modo principal. El modo principal es: el sistema vigila y avisa.

## 6. Qué YA existe y se reutiliza (no reconstruir)

- `rate_seasons` / `rate_season_dates` / `unit_rates`: las 13 temporadas → alimentan la generación de `price_calendar`
- El motor de resolución de precio (resolver.ts): ya sabe calcular una tarifa de una fecha → se usa para poblar el calendario
- El scraper (Booking funciona, PXSOL a arreglar): alimenta las señales de competencia
- La lógica de decisión (decision.ts): ya evalúa señales → se usa en el motor de vigilancia
- BDVL: el flujo de aprobación → se usa para aprobar oportunidades

**Lo que falta CONSTRUIR (el hueco real):**
1. Tabla `price_calendar` + proceso que la puebla desde las temporadas
2. Tabla `pricing_opportunities`
3. El motor de vigilancia (cron que compara vigente vs. señales y detecta oportunidades)
4. Las alertas proactivas por Telegram
5. Vista/edición del calendario (dashboard)

## 7. Orden de construcción sugerido

1. `price_calendar` + poblarla desde las 13 temporadas (primero tener la grilla de precios)
2. Verificar que la grilla es correcta (cada fecha su tarifa según temporada/día) — el propietario la revisa
3. Arreglar los scrapers (PXSOL parse + Booking orquestación) para tener señales reales
4. Motor de vigilancia + `pricing_opportunities`
5. Alertas proactivas por Telegram
6. Vista de calendario en dashboard

## 8. Guardrails (heredados de DOMAIN_KNOWLEDGE)

- El precio vigente nunca supera el techo sin marcarlo
- Sin dato real de competencia para una fecha → no inventar, usar histórico/temporada y declararlo
- Toda modificación de precio pasa por aprobación del propietario (BDVL)
- El sistema propone, el propietario decide

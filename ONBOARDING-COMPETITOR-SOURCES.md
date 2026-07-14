# Onboarding de un cliente nuevo — Fuentes de competencia y techo (SIN código)

**Fecha:** 2026-07-13
**Principio:** ATLAS es un SaaS multi-tenant. Todo lo específico de un propietario vive en
DATOS (`competitor_sources`, `pricing_ceilings`), nunca en código. Un cliente nuevo se
configura sin que nadie escriba una línea. Si algo de esto requiere código, el diseño está mal.

## El flujo (3 pasos, solo datos)

### 1. Agregar sus fuentes de competencia (URL + tipo)
Insertar filas en `competitor_sources` eligiendo un **adapter_type existente** (nunca uno nuevo por cliente):

| adapter_type | Cuándo usarlo | config (jsonb) |
|---|---|---|
| `booking` | Competencia de Booking.com (Bright Data mantiene el scraper) | `{ "dataset_id": "<id del dataset de Booking en Bright Data>" }` |
| `direct_web` | Motor de reservas de una web propia/de complejo que expone un endpoint JSON por fecha | `{ "endpoint": "https://.../avail?in={check_in}&out={check_out}", "pricePath": "results[].price", "namePath": "results[].name" }` |
| `direct_web` (motor con sesión/JS) | Motores tipo Reserva Directo/Octorate que exigen login o JS | `{ "requires_js": true }` → el sistema lo marca como "no fetch directo" y usa la tarifa histórica como ancla (Principio 15). Necesita Playwright o Bright Data para leer precios reales. |

Ejemplo (por SQL o, a futuro, por el dashboard):
```sql
insert into competitor_sources (property_id, adapter_type, name, url, config, is_ceiling, active)
values ('<property_id>', 'booking', 'Booking — mi zona',
        'https://www.booking.com/searchresults.html?ss=<mi+destino>',
        '{"dataset_id":"<dataset>"}', false, true);
```

### 2. Marcar cuál es su techo (ADR-014) — tres opciones, todas configurables
- **(a) Una fuente scrapeada como techo:** poné `is_ceiling = true` en una fila de `competitor_sources` (ej. el complejo vecino más directo). El techo = su precio scrapeado **por fecha**.
- **(b) Un valor fijo:** una fila en `pricing_ceilings` con `source_type='fixed_value'`, `ceiling_usd=<valor>` (ej. "la semana en Brasil equivalente").
- **(c) Un % sobre la mediana de competencia:** `source_type='percent_over_median'`, `ceiling_pct=1.10` (110% de la mediana).

El motor toma el techo **más binding** (menor) y **nunca recomienda por encima sin marcarlo**.

### 3. Mapear cada competidor a sus unidades
Ya soportado (Fase 1): `competitor_scraper_results.applies_to_unit_id` + `adjustment_pct`
(ajuste de comparabilidad §1.6). El scraper hereda `applies_to_unit_id` de la fuente
(`competitor_sources.applies_to_unit_id`) o lo deja general (null).

## Qué corre el sistema (sin Event Bus)
- **Frecuencia (ARCH-0027):** diario para los próximos 30 días, semanal para fechas lejanas.
  *Pendiente registrado:* frecuencia adaptativa por booking_pace (aún no calculable, faltan reservas).
- **Budget:** créditos por **organización** (`scrape_credits`, 5.000/mes free tier de Bright Data).
- **Persistencia:** `competitor_scraper_results` con `stay_date` (fecha de la ESTADÍA, no de scraping) → comparación de competencia POR FECHA.
- **Fallo:** si una fuente no responde, se **dice explícito** ("no pude obtener precios de X, uso tarifa histórica como ancla") y **nunca se inventan precios** (§3.2).

## Requisitos externos (que el operador/propietario provee, no código)
- **Booking:** cuenta free de Bright Data (5.000 créditos/mes, sin tarjeta) → `BRIGHTDATA_API_KEY` + `dataset_id` en la fuente.
- **Motor propio con sesión/JS (ej. Reserva Directo):** un endpoint JSON consultable, o Playwright headless / Bright Data. Sin eso, el sistema degrada honestamente a tarifa histórica.

## Prueba de que es SaaS
LindaBay se cargó como el **primer cliente**, no el único: sus fuentes están en
`competitor_sources` (Reserva Directo marcada `is_ceiling`, Booking de su zona), su techo
configurable. Un segundo cliente se agrega repitiendo los 3 pasos — sin tocar código.

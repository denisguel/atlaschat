# SPEC — Modelo de Datos de Pricing (el que faltó desde el inicio)

**Fecha:** 2026-07-11
**Estado:** Draft — requiere completar 2 puntos con el propietario antes de implementar
**Por qué existe:** los 60 documentos definieron la ESTACIONALIDAD como concepto ("weekend optimization", "seasonal adjustments") pero NUNCA como estructura de datos. Property (Domain Model 04) solo tenía un precio único. Este es el hueco que hacía que el Revenue Agent devolviera `base_price_usd` sin distinguir temporada ni día. Esta spec lo cierra.

**Principio de diseño:** el modelo debe servir a CUALQUIER propietario, no solo a LindaBay. Las temporadas y tarifas son datos configurables por propiedad, no hardcodeadas.

---

## 1. Modelo de tarifas — DOS modelos de venta coexistiendo

El hallazgo clave del propietario: no hay un solo modelo de precio. Cada temporada usa **uno de dos modelos de venta**:

- **MODELO NOCHE** (`per_night`): precio por noche, diferenciado L-J (weekday) vs V-D (weekend). Usado en: resto del año (cada mes con sus valores), y diciembre antes del 20 (sin mínimo de 7 noches).
- **MODELO PAQUETE** (`package`): precio por estadía completa de N noches, no por noche. Usado en: alta temporada (dic post-20, navidad, enero, febrero, marzo hasta mediados = por semana/7 noches), vacaciones de invierno (paquete 4 noches Y paquete 7 noches), findes largos (valor del paquete completo).

### Tabla `rate_seasons`
```sql
rate_seasons (
  id uuid PK,
  property_id uuid FK -> properties,   -- RLS por property
  name text,                            -- "Enero", "Vacaciones invierno", "Finde largo agosto", "Base octubre"
  pricing_model text,                   -- 'per_night' | 'package'
  priority int,                         -- resuelve solapamientos (mayor gana)
  min_nights int,                       -- ej: 7 para alta temporada, null si no aplica
  created_at timestamptz
)
```

### Tabla `rate_season_dates` (qué fechas cubre)
```sql
rate_season_dates (
  id uuid PK,
  season_id uuid FK -> rate_seasons,
  start_date date,        -- null si recurrente
  end_date date,          -- null si recurrente
  recurring_rule text,    -- null si rango fijo. ej: "FRI,SAT,SUN"
  year int,               -- para fechas que cambian por año (semana santa, findes largos)
  created_at timestamptz
)
```

### Tabla `unit_rates` — cubre AMBOS modelos
```sql
unit_rates (
  id uuid PK,
  unit_id uuid FK -> units,
  season_id uuid FK -> rate_seasons,
  day_type text,          -- MODELO NOCHE: 'weekday'|'weekend'. MODELO PAQUETE: 'all'
  package_nights int,     -- MODELO PAQUETE: 4, 7, etc. MODELO NOCHE: null
  price_usd numeric,      -- por noche (per_night) o total del paquete (package)
  created_at timestamptz
)
```

**Cómo resuelve el precio de una consulta (fecha inicio + fecha fin):**
1. Identificar qué `rate_season` cubre las fechas (por `rate_season_dates`, solapamiento por `priority`)
2. Leer `pricing_model` de esa temporada:
   - **per_night:** para cada noche, determinar day_type (L-J/V-D) → sumar `unit_rates.price_usd` de cada noche
   - **package:** buscar el `unit_rates` con `package_nights` que matchee la duración (o el más cercano) → ese es el precio total. Validar `min_nights`.
3. Si la consulta cruza temporadas, resolver noche por noche con la temporada que aplica a cada una.

**Ejemplos LindaBay:**
- Octubre (per_night): Mono L-J $X / V-D $Y
- Enero (package, min 7): Mono paquete 7 noches $Z (total, no por noche)
- Vacaciones invierno (package): Mono paquete 4 noches $A, paquete 7 noches $B
- Diciembre pre-20 (per_night): Mono L-J / V-D — sin mínimo
- Finde largo (package): Mono paquete 3 noches $C

---

## 2. Competidores — origen de datos

| Fuente | Método | Estado |
|---|---|---|
| Booking.com | Scraper (ya existe scraper base en el proyecto) | Fase 2 — conectar scraping en vivo por fecha |
| lindabay.com.ar (administración) | **Scraping indirecto del botón "Reservas"** — la web tiene pricing dinámico accesible vía el flujo de reserva. Se scrapea consultando fechas en ese motor. | Fase 2 — scraper del motor de reservas de lindabay.com.ar |

Cada competidor scrapeado alimenta `competitor_scraper_results` (ya existe), pero necesita:
- `date` de la estadía consultada (no solo `scraped_date`) → para comparar competencia POR FECHA, no en general
- `applies_to_unit` (ya existe)

**Frecuencia de scraping:** a definir. Propuesta: diario para fechas próximas (30 días), semanal para fechas lejanas.

---

## 3. Techo de precio (ADR-014)

- Fuente para LindaBay: lindabay.com.ar
- **Configurable por propietario:** cada uno define su techo (una web, un valor fijo, o "costo de destino alternativo" como Brasil).
- Tabla `pricing_ceilings (property_id, source_type, source_url_or_value, note)`.
- El agente NUNCA recomienda por encima del techo sin marcarlo explícitamente.

---

## 4. Interacción esperada (el flujo que el propietario quiere validar)

**Consulta de disponibilidad + cotización en lenguaje natural:**

```
Propietario: "precio para 4 personas, fin de semana largo de agosto"
              ↓
[L1] extrae: pax=4, fechas=(rango finde largo agosto)
              ↓
[Disponibilidad L0] filtra unidades con capacity>=4 Y libres esas fechas
              ↓ (devuelve las 2 unidades, no una)
[Pricing por unidad] para cada unidad:
   - resuelve temporada (finde largo) × day_type de cada noche
   - compara vs competidores de esa fecha (ajustado por adjustment_note)
   - aplica techo
              ↓
Respuesta: "Para 4 personas, 15-18 agosto (finde largo):
   • Monoambiente: $145/noche × 3 = $435. Competencia (LINDA STUDIO, link) $150. Sugiero mantener.
   • Departamento: $180/noche × 3 = $540. Competencia (LINDA DUPLEX, link) $175. Sugiero bajar a $170.
   Presupuesto total sugerido: [ambas opciones].
   ¿Aprobás, ajustás, o querés que analicemos alternativas?"
              ↓
Si el propietario no aprueba ni rechaza → CONVERSA con la IA (Fase 2 AI Orchestrator):
   "¿y si subo el finde?" / "¿cuánto pierdo si dejo el mono en 130?" → el agente razona
```

**Esto requiere (en orden):**
1. Modelo de tarifas (§1) — **Fase 2, base de todo**
2. Competencia por fecha (§2) — Fase 2
3. Pricing multi-unidad cruzado con capacidad — Fase 1.5, ya casi está
4. AI Orchestrator para la conversación libre (§4 último paso) — Fase 2, el componente grande

---

## 5. Calendario de precios (visualización)

Dashboard (NO Telegram, por ARCH-0023). Vista mensual que muestra, por unidad y por día, el precio resuelto (temporada × day_type), con los precios propuestos/aprobados por el agente. Es la tabla que el propietario pidió "para poder probar".

---

## 6. Estrategia de carga de datos (definida con el propietario)

**Los valores de tarifas se pueblan de DOS formas, no excluyentes:**
1. **Carga manual** cuando el propietario los define (vía ingestor o dashboard)
2. **Scraping + pricing:** el motor de lindabay.com.ar (botón Reservas, pricing dinámico) se scrapea indirectamente y va completando los valores de referencia

Esto significa: el sistema NO requiere que el propietario cargue las 13 temporadas a mano el día 1. Puede arrancar con lo que scrapea de su propia web + Booking, y el propietario ajusta/confirma.

**Seed inicial LindaBay (a completar por el propietario cuando quiera, o dejar que el scraping lo llene):**
Estructura confirmada:
- Alta temporada por PAQUETE/semana: diciembre post-20, navidad, enero, febrero, marzo hasta mediados (cada uno valor semanal distinto)
- Diciembre pre-20: per_night L-J / V-D (sin mínimo)
- Vacaciones invierno: paquetes de 4 y 7 noches
- Findes largos: paquete del finde
- Resto de meses: per_night, L-J un valor / V-D otro, distinto cada mes

## 7. Orden de construcción (Fase 2)

1. Tablas del modelo de tarifas (§1) — base de todo
2. Resolver-precio-de-consulta (el algoritmo §1) — motor determinístico L0
3. Pricing multi-unidad cruzado con capacidad (§4) — devuelve TODAS las unidades que sirven
4. Scraper de lindabay.com.ar (motor de reservas) + Booking por fecha (§2)
5. AI Orchestrator: intención (disponibilidad/cotización) + conversación libre (§4 último paso)
6. Calendario de precios en dashboard (§5)

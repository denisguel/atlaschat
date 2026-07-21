# ESTADO DEL PROYECTO ATLAS — Tablero Único de Verdad

**Última actualización:** 2026-07-21
**Mantenimiento:** este archivo se actualiza al cerrar CUALQUIER tarea, sin pedirlo (instrucción del founder). Es la vista de secuencialidad y objetivos.
**Fuente de verdad de decisiones:** los ADR/DOC referenciados en cada fila. Si una fila y su ADR se contradicen, gana el ADR y se corrige la fila.

## Cómo leer

- **Estados:** `HECHO` · `EN CURSO` · `PENDIENTE` · `DIFERIDO` (a fase posterior, con ADR que lo respalda).
- **Fases** (marco operativo de los ADR, no las Etapas 0-7 del roadmap ARCH-0019):
  - **Fase 0** — Fundación: documentación curada, data model, infraestructura, seguridad.
  - **Fase 1** — Revenue Agent únicamente (ADR-010). Scope activo.
  - **Fase 2** — Context Builder, Brains, Multi-Agent, Booking, Growth, Competitor Scout.
  - **Fase 3+** — Visión completa NeuralCore (RFC-0001..0010).
- **Verificación:** ningún ítem se marca `HECHO` sin evidencia real (migración aplicada, fila en DB, test verde, output real). Ver política en CLAUDE.md.

---

## FASE 0 — Fundación

| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Reconciliación documental (69 origen → 53 canónicos + 17 archivados) | HECHO | 2026-07-03 | CHANGELOG_RECONCILIATION.md |
| Data Model Specification | HECHO | 2026-07-03 | DOC-0032 |
| Security & Multi-Tenancy Handbook | HECHO | 2026-07-03 | DOC-0031 |
| Testing Strategy | HECHO | 2026-07-03 | DOC-0033 |
| Proyecto Supabase `xlhafthdfbrisvxbowjt` (atlas-fase1) | HECHO | 2026-07-04 | CLAUDE.md (infra) |
| 14 migraciones + RLS en toda tabla + Custom Access Token Hook (claim `organization_id`) | HECHO | 2026-07-04 | DOC-0032, DOC-0031 §3 |
| Decisión de rewrite sobre DOC-0032 | HECHO | 2026-07-04 | ADR-013 |
| Retiro formal de la app vieja (Gemini + Pages Router) a `_ARCHIVE` | HECHO | 2026-07-08 | ADR-013 AI#4 |

---

## FASE 1 — Revenue Agent (scope activo)

### Núcleo construido
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Auth + membership (@supabase/ssr, cookies) | HECHO | 2026-07-05 | DOC-0031 §4 |
| CRUD properties + units (`/console`, `/console/properties`) | HECHO | 2026-07-05 | DOC-0032 |
| Capa de mensajería abstracta (Channel) + adapter Telegram | HECHO | 2026-07-06 | ADR-003-A |
| Credenciales de canal cifradas (AES-256-GCM) en `property_integrations` | HECHO | 2026-07-06 | DOC-0031 §4 |
| Revenue Agent: contexto mínimo → circuit breaker → L3 → BDVL → execution_logs | HECHO | 2026-07-07 | ADR-010-B, ARCH-0027, ADR-010-C, ARCH-0028 |
| Abstracción de proveedor IA (Claude/Groq/Gemini vía `REVENUE_AGENT_PROVIDER`) | HECHO | 2026-07-07 | ADR-008, ADR-011, ADR-011-A |
| BDVL como policy engine (riesgo por % cambio; nunca auto-aplica) | HECHO | 2026-07-07 | ADR-010-C, ARCH-0022 |
| Comandos Telegram: `/pricing`, `/aprobar`, `/rechazar`, `/disponibilidad` | HECHO | 2026-07-07 | ADR-003-A, ARCH-0028 |
| Disponibilidad: intent L1 + query L0 determinística (capacidad + solape reservas) | HECHO | 2026-07-07 | ADR-010-B |
| Ingesta histórica real (reservas CSV/JSON + Excel, dólar blue por año) | HECHO | 2026-07-08 | ADR-011-A (fx.ts) |
| Pricing de **ambas unidades** cuando no se especifica | HECHO | 2026-07-08 | — (mejora Fase 1) |
| Competidores por unidad con comparabilidad (`applies_to_unit_id`, `adjustment_note`) | HECHO | 2026-07-08 | ADR-015 (carga manual), DOC-0031 §3 |
| Transparencia en recomendación (competidor: nombre+precio+link+unidad+nota) | HECHO | 2026-07-08 | — (mejora Fase 1) |
| Guardrail no-inventar m²/características (usa nota o compara crudo y lo aclara) | HECHO | 2026-07-08 | ARCH-0028 |
| Fix: pricing COMPARA base vs competidores (ajustados) y recomienda subir/bajar/mantener — ya no repite el base | HECHO | 2026-07-11 | RFC-0008 (bug de instrucción, evidencia en dump-prompt.mts) |
| Ingesta de competidores reales de LindaBay (10 filas cargadas) | HECHO | 2026-07-08 | ADR-015 |
| Webhook Telegram serverless (Vercel) reemplaza polling | HECHO | 2026-07-08 | ADR-003-A |
| Deploy a Vercel (atlas-mvp-pink.vercel.app) + webhook registrado | HECHO | 2026-07-08 | DOC-0017 |
| Tests de lógica de negocio (Vitest, 15 verdes) | HECHO | 2026-07-08 | DOC-0033 |
| Round-trip verificado en producción (execution_logs + bdvl_decisions reales) | HECHO | 2026-07-08 | ADR-010-C |

### Abierto en Fase 1
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Techo de precio: ajuste **manual** del propietario al confirmar (BDVL requires_confirmation) | EN CURSO | — | ADR-014 (Fase 1 vigente) |
| Dashboard web básico (existe login + console + CRUD; interfaz principal es Telegram) | EN CURSO | 2026-07-05 | ADR-010 (scope) |
| Unificar etiqueta `source` en pricing_history (`ai_recommendation` vs `agent`/`manual`) | PENDIENTE | — | issue spawneado (task_07321159) |
| Criterio de salida Fase 1 → Fase 2 (métrica de LindaBay que dispara Context Builder) | PENDIENTE | — | ADR-010 AI#3 |
| Actualizar ARCH-0018 (roadmap) con el scope Fase 1 explícito | PENDIENTE | — | ADR-010 AI#4 |

### Divergencias registradas (implementación ≠ ADR original, ver GAP_ANALYSIS)
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Scraper de competidores en vivo: ADR-010 lo listaba en Fase 1 → diferido a Fase 2 (carga manual) | DIFERIDO | 2026-07-08 | ADR-015 (supersede parcial de ADR-010) |
| Workflow engine: ADR-006 manda n8n; Fase 1 usa Next.js + webhook Vercel, **sin n8n** | PENDIENTE (formalizar) | — | ADR-006 (sin ADR que registre la divergencia — ver GAP) |

---

## FASE 2

### Parte 1 — Motor de pricing correcto (HECHO)
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Tablas del modelo de tarifas (rate_seasons, rate_season_dates, unit_rates) + RLS | HECHO | 2026-07-11 | SPEC-PRICING-MODEL §1, DOC-0031 §3 |
| Motor resolver-precio L0 (per_night L-J/V-D, package, cruce de temporadas, desglose) | HECHO | 2026-07-11 | SPEC-PRICING-MODEL §1 |
| Cotización multi-unidad cruzada con capacidad (devuelve TODAS las que sirven) | HECHO | 2026-07-11 | SPEC-PRICING-MODEL §4 pto 3 |
| Interfaz `/pricing "N personas + fechas"` → motor de tarifas (por comando) | HECHO | 2026-07-11 | SPEC-PRICING-MODEL §4 |
| Guardrail: el motor nunca inventa tarifa ausente (lo dice explícito) | HECHO | 2026-07-11 | SPEC-PRICING-MODEL §1 |
| Tests del motor (9: per_night, package, min_nights, cruce, guardrail, multi-unidad) | HECHO | 2026-07-11 | DOC-0033 |

### Parte 2 — Inteligencia de decisión (EN CURSO)
| Ítem | Estado | Fecha | Respaldo |
|---|---|---|---|
| Ingestor de RESERVAS (Excel/CSV arbitrario, mismo pipeline L1 que precios): mapeo L1 de columnas + unidad por alias (matchesHint) + normalización determinista + preview→--apply. Migración `reservations`+booking_date/pax/fx. §5.2: ocupación/gaps sí, pace histórico NO (booking_date NULL). Reservas NUEVAS capturan booking_date=hoy → pace desde ahora | HECHO | 2026-07-17 | tsc=0, 92/92; preview real: columnas raras mapeadas, alias→unidad, sin-booking y cancelada bien clasificadas, ambiguas marcadas |
| Paso 1a: inferir unit_rates desde pricing_history real (source='inferred_from_history') | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §3.3 |
| Paso 1b: reconectar competencia real con ajuste de comparabilidad numérico (adjustment_pct) | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §1.6 |
| Paso 2: motor de decisión (analyze.ts): tarifa + competencia ajustada + ocupación histórica + techo → subir/bajar/mantener con justificación | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §2.3 |
| Tabla pricing_ceilings (techo por propiedad) + RLS | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §1.4 |
| Guardrail: competencia solo decide dirección si es de la fecha consultada (fix de BUG) | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §2.2 |
| Umbrales operativos: booking pace (ratios), gap por temporada, revenue simulator, maximize_revenue | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §5.1-5.4 (decision.ts, testeado) |
| Explicabilidad §5.5 (7 elementos) + bandas de autonomía §5.6 OFF (BDVL requires_confirmation) | HECHO | 2026-07-11 | DOMAIN_KNOWLEDGE §5.5-5.6 |
| Paso 3: AI Orchestrator conversacional (router de intención sin comandos + memoria de hilo + negociación + registro de reservas) | HECHO | 2026-07-13 | DOMAIN_KNOWLEDGE §3.4 (agent_conversations, router/respond/memory) |
| Registrar reserva desde la conversación (created_at = fecha de reserva → habilita pace futuro) | HECHO | 2026-07-13 | DOMAIN_KNOWLEDGE §5 pto 5 |
| Feedback uso real #1: reglas de negocio al BACKEND determinista, no al LLM (min_nights/package_nights coexisten) | HECHO | 2026-07-13 | ARCH-0026 |
| Feedback #2: decisión = revenue management, no mediana (anomalías sobre precio crudo + posición histórica + ancla en tarifa) | HECHO | 2026-07-13 | DOMAIN_KNOWLEDGE §2.3/§5.3 |
| Feedback #3: memoria de hilo por compresión a hechos + herencia determinística de entidades + aliases de unidad | HECHO | 2026-07-13 | Principio 6 |
| Feedback #4: formato Telegram compacto (negrita, un competidor por línea, no repetir) | HECHO | 2026-07-13 | ARCH-0023 |
| Fase 3 estabilidad #1: anomalías por divergencia estadística (IQR/Tukey) — casos A/B + muestra <4 low_confidence | HECHO | 2026-07-13 | RFC-0008 (test) |
| Fase 3 #2: anti-silent-failure (mensaje intermedio a 8s + try/catch que siempre responde) + maxDuration=30 | HECHO | 2026-07-13 | ARCH-0025/0027 |
| Fase 3 #3: higiene de contexto — al LLM solo datos filtrados (no excluidos); prohibido decir "no calculado" con target; herencia SSOT | HECHO | 2026-07-13 | ARCH-0029 / Principio 10 |
| Fase 3 #4: toda escritura por BDVL (registro de reserva graba bdvl_decisions) | HECHO | 2026-07-13 | ARCH-0022/0026 |
| Paso 4: arquitectura del scraper por fecha (adapters por tipo, competitor_sources, budget/org, techo 3 tipos, stay_date) | HECHO | 2026-07-13 | ARCH-0026, ADR-014 (ONBOARDING-COMPETITOR-SOURCES.md) |
| Paso 4: fetch de precios REALES de Booking vía Bright Data (dataset gd_mdy9ld3p1e0oqlj9g4) + competencia por fecha en analyze | HECHO | 2026-07-14 | key activa; evidencia real (Linda Bay 202 $136.67, 205 $125 por stay_date) |
| Paso 4: SSOT `competitor_prices` (serie temporal por stay_date) + adapter_type enum (BOOKING_MANAGED/WEB_DIRECT_API) | HECHO | 2026-07-14 | RFC-0008 |
| Paso 4: WEB_DIRECT_API config-driven (PXSOL) + KPIs (RatePerDay sin promediar, availability→pace, lead_time, SKU→unidad) | HECHO | 2026-07-14 | ARCH-0026, RFC-0008 (parse probado con fixture) |
| Paso 4: PXSOL fetch en vivo (Reserva Directo, el techo) | HECHO | 2026-07-14 | GET+Referer+SearchID capturado; SkuID 14014→UF214 = USD 175.44/noche real (250k ARS/blue) en competitor_prices |
| Paso 4: flujo dos-pasos (init SearchID dinámico) + config .json + loader + cron resiliente | HECHO | 2026-07-14 | ARCH-0026/0027; testeado con fetch mock (init→SearchID→list6, ARS→USD, SKU→unidad) |
| Paso 4: PXSOL DINÁMICO por fecha (insert real → SearchID → list6) | HECHO | 2026-07-14 | api-1-eb-web.pxsol.io/search/insert (302+Location); precios reales distintos por fecha (jul16 USD129.82 / jul20 USD175.44 → UF214) |
| Paso 4: cron de acumulación activado (Booking + PXSOL dinámico) → serie temporal + marketPace | EN CURSO | 2026-07-14 | scripts/cron-scrape.mts; falta agendarlo (GitHub Actions/cron de máquina, diario) |
| §6.4 Fuente de verdad según ANTICIPACIÓN + datos discontinuos: fecha lejana (nadie publicó) → recomendación por histórico declarado + "competencia/techo no publicado aún"; fecha cercana → competencia/techo real scrapeado. Techo de MERCADO (is_ceiling scrapeado) capa duro; configurado = estimado (NUNCA como real). Checklist de fuentes (✔/✘). Cron re-scrapea vacías + muestreo lejano (lead times alta temporada) | HECHO | 2026-07-17 | DOMAIN_KNOWLEDGE §6.4/§6.1; evidencia real: enero=far (techo estimado, "no publicado"), jul-20=near (techo real 175.44). tsc=0 |
| §6.1 Contexto monetario EN el dato: `pricing_history.fx_ars_usd` + `fx_precision` ('exacto'\|'derivado'), RLS heredada. Backfill 366 filas (2013-2026) desde fx.ts marcado 'derivado' (promedio anual = aproximado). Precios nuevos capturan fx vía `fxForDate()`. La explicabilidad declara la precisión real; USD NOMINAL explícito (normalización por poder de compra NO construida: requiere índice de inflación, fuera de alcance) | HECHO | 2026-07-17 | migración aplicada; 0 filas sin fx; evidencia: "Precisión: 34 derivada/s (promedio anual del blue — aproximado)" |
| Diagnóstico de respuestas Telegram deficientes (4 casos, dato crudo: router/motor/BDVL) | HECHO | 2026-07-15 | evidencia real por caso; ningún quiebre en BDVL — 3 motor, 1 feature inexistente |
| Fix CASO 1: `resolveUnits` — el filtro capacity>=pax NUNCA se saltea con hint; conflicto de capacidad se avisa con alternativas (no se ofrece unidad chica) | HECHO | 2026-07-15 | DOMAIN_KNOWLEDGE §3.2; test (pax5+mono→conflict, pax2+mono→match) + corrida real UF214/UF508 |
| Fix CASO 2: comparabilidad de competidores por SKU o capacidad; los no mapeables se EXCLUYEN (fin del "arrastra todo Booking" por unit_id NULL). Migración `comparable_capacity` + adapter Booking captura ocupación | HECHO | 2026-07-15 | DOMAIN_KNOWLEDGE §1.6; `isComparableCompetitor` testeado + corrida real (mono cap4 solo trae SKU-mono + cap4) |
| CASO 3 / Problema 1: EDITOR DE REGLAS por Telegram (BDVL-gated). intent `modify_rate` + `extractRateEdit` (L2, estructura estricta) + `proposeRateEdit` (L0, valida+BDVL requires_confirmation) + `applyRateEdit` (escribe rate_seasons/unit_rates SOLO tras /aprobar, approved_by real). Guard: /aprobar sin id con >1 pendiente pide el id | HECHO | 2026-07-16 | e2e real en temporada descartable: mensaje→propuesta con id→/aprobar→"7n eliminado; 3n→USD250; mínimo→3" + verificación DB + BDVL approved. tsc=0, 83/83 |
| Fix cotización (verificado con el MENSAJE REAL del LLM, no en aislamiento): (2) "N personas" ya no queda pegado a una unidad heredada → muestra todas por capacidad; (3) fallback manual filtra por unidad + payload capa a top-5 más cercanos con total; (4) techo visible ("Techo: USD X según fuente") | HECHO | 2026-07-16 | corrida e2e: UF214+UF508, 5 competidores + "9 comparables", "Techo USD 180" |
| Fix CASO 4: `resolveCeiling` lee el techo scrapeado de `competitor_prices` (SSOT) por `source_id` de la fuente `is_ceiling` + comparabilidad. Config-driven (flag is_ceiling), NO hardcodeado a LindaBay/PXSOL (ADR-014) | HECHO | 2026-07-16 | corrida real: techo UF214 = USD 129.82 (jul16) / 175.44 (jul20) desde "Reserva Directo" is_ceiling; antes null. Test ceiling.ts (5 casos) + predicado compartido `comparability.ts` (sin ciclo) |
| Auditoría Telegram A (seguridad): autorización de chats. Tablas `telegram_link_tokens` + `telegram_links` (RLS), gate en `routeInbound` (solo chats vinculados a la org operan), `/vincular <token>` single-use generado en `/console/telegram`, `approved_by` = user_id real | HECHO | 2026-07-16 | corrida real e2e: no-vinculado→rechazado, token single-use, aislamiento por org, approved_by real. Test telegram-auth (9 casos) |
| Auditoría Telegram B (formato): el ADAPTER arma el formato (no el LLM) en HTML seguro (parse_mode=HTML + escaping `&<>`), negrita real, fallback a texto plano. Fin de asteriscos literales; caracteres especiales no rompen el envío | HECHO | 2026-07-16 | test telegram-format (6 casos, incluye nombre con guion/paréntesis y URL con especiales) |
| Auditoría Telegram C (dead code): removido `handleAvailability` (0 refs, cubierto por Orchestrator). `handlePricing`/`fmt*` NO se borran: son la superficie de dry-run de scripts (show-pricing/show-quote) — auditoría previa corregida | HECHO | 2026-07-16 | grep de referencias; tsc=0 + 81/81 tras la limpieza |
| Ingesta de reservas reales (30 reservas, 98 noches) + fila marzo completada en SSOT | HECHO | 2026-07-17 | preview→upsert; ARS→USD blue derivado; dedup verificado |
| Ocupación real + gap nights (§5.1/§2.3): `occupancyInWindow` + `detectGapNights` (puras), ocupación del mes + periodBooked + detección de gaps por temporada + señal direccional por ocupación (proxy del pace) + confianza low→medium/high. dataGaps deja de listar ocupación/gaps | HECHO | 2026-07-17 | e2e real: mar 36% periodBooked=true; finde largo ago tarifa+regla+detección; tsc=0, 99/99 (7 tests nuevos) |
| Scraper de Horizonte (ADR-015): cron HOY..+180d, backoff (vacía→3d, con datos→7d) en `competitor_scrape_state`, alimenta `competitor_prices` (SSOT); `effectiveCeiling` (decision.ts) — el techo scrapeado desplaza al configurado; property_id EXPLÍCITO (aislamiento); cambio de techo ≥15% → execution_logs (BDVL) | HECHO | 2026-07-18 | e2e real: sin fuga entre propiedades, backoff 3d, cambio +38% highRisk logueado, effectiveCeiling capa con scrapeado. tsc=0, 109/109 |
| Resiliencia proveedor gratuito (Telegram): CADENA con fallback Groq→Nemotron(NVIDIA NIM)→Gemini; retry cubre 429/over-capacity/timeout+red; ProviderSaturatedError → mensaje claro (nunca mudo, incl. webhook). DeepSeek descartado (API paga, 402). Docs: ADR-011-A + .env.example | HECHO | 2026-07-18 | tsc=0, 115/115; e2e: Groq falla→Nemotron responde en vivo, los 3 caídos→mensaje de saturación |
| Scheduler del Scraper de Horizonte: GitHub Actions (`.github/workflows/scrape-horizon.yml`) diario 06:00 UTC + dispatch manual. Secrets en GitHub Secrets. Free-tier: fuentes baratas primero (PXSOL antes que Booking), --max 40, tope mensual `SCRAPE_CREDITS_LIMIT` (default 3000), log de créditos por corrida, fallo sistémico→rojo | HECHO | 2026-07-18 | workflow activo en main; tsc=0, 115/115 |
| PXSOL "sin tarifa publicada" (disponibilidad 0 + precio centinela 99999999999 = carga discontinua, NO error) → adapter devuelve `ok:true` count 0; el cron separa fallas (ok:false) de vacías | HECHO | 2026-07-19 | commit 9ab3d25; corrida 40 escaneadas/0 con datos diagnosticada (no era falla, era no-publicado) |
| §3.4 CASO síntoma 3: nombrar una temporada sin fechas ("vacaciones de invierno") → ATLAS ASUME sus fechas cargadas (`resolveSeasonMention`, determinístico) en vez de pedir fechas conocidas | HECHO | 2026-07-19 | commit 63aed00; test season-dates (5 casos) |
| §3.4 modify_rate lee FECHAS como contexto de temporada: "eliminar restricción desde hoy al 3/8" → `seasonCoveringDate` resuelve "Vacaciones invierno" (antes: "no sé qué temporada"). extractRateEdit mapea "eliminar/sacar/quitar restricción / sin mínimo"→set_min_nights:1 y season_hint es NOMBRE-only. §3.2 honestidad: en temporada por PAQUETE avisa que bajar el mínimo no habilita noches sueltas sin tarifa por noche | HECHO | 2026-07-19 | commit 244699e; e2e real 3 frases (invierno 4→1, eliminar por fechas, sacar mínimo); tsc=0, 120/120 |
| Ingesta de TEMPORADAS HISTÓRICAS (13 temporadas, 2013-2026) desde "Disponibilidad LindaBay": parser de calendario por RUNS DE COLOR (cada reserva = tramo del mismo relleno en fila MONO=UF214/DUPLEX=UF508; nombre en 1ra celda; largo=noches; gris/negro=familia/uso propio) para 4 hojas históricas + tabla de detalle (importe total/reserva/saldo, fechas, unidad por texto del calendario) para ESTADIAS DETALLE. Excel nuevo con todas las reservas + ingesta a `reservations` (450 guests creados, ARS→USD blue por año, dedup unidad+check_in). Filtra artefactos (feriados/sombreado), merge de fragmentos por cruce de mes, clamp de solapes | HECHO | 2026-07-20 | scripts/parse-historicas.cjs + ingest-historicas.mts; **514 reservas nuevas** insertadas, 0 duplicados, 0 solapes, 520/521 unidad resuelta; verificado en DB (544 total, UF214:244/UF508:300) |
| Ingesta de exports TABULARES limpios (Reservas 2013.xlsx + Reservas hist.xlsx): una fila por reserva con PAX + importes ARS + fecha reserva/saldo. Dedup contra calendario por (unidad+check_in); reconciliación de solapes (el tabular es autoritativo: 34 filas de calendario difusas borradas, 12 solapes tabular clampeados) | HECHO | 2026-07-20 | scripts/ingest-tabular.mts; **84 nuevas** (rellenan 2013-2015 que el calendario no tenía); estado final DB: **594 reservas, 0 solapes, 0 duplicados**, 134 con precio, 45 con fecha de reserva, 15 años |
| Fix STARVATION de Booking en el cron de horizonte: el loop externo era por FUENTE, así que con "barato primero + `--max` + break" PXSOL consumía todo el presupuesto recorriendo las 180 fechas y Booking NUNCA se ejecutaba. Ahora la FECHA es el loop externo y las fuentes el interno (barato-primero se mantiene DENTRO de cada fecha). Suma `sampledHorizon()` (denso los primeros 14d + cada 7d hasta el horizonte, para alcanzar alta temporada en cada corrida) y el flag `--dates d1,d2` de verificación dirigida, que saltea el backoff | HECHO | 2026-07-20 | commits 6472b99 + 5987d2f; corrida real `--dates 2026-07-24,25,26`: **3 escaneadas / 3 con datos / 0 fallas → 3 filas nuevas en `competitor_prices`** (Linda Bay 202, USD 140/noche); Booking pasa de 3 a 6 filas. `competitor_scrape_state` registra a Booking intentando 2026-08-15 / 2026-12-28 / 2027-01-05 / 2026-10-15 / 2026-10-18 / 2027-01-10 — fechas lejanas que antes nunca alcanzaba. tsc=0, 120/120 |
| PXSOL (`WEB_DIRECT_API`, adapter `lib/scraper/adapters/direct-web.ts`): devuelve 0 tarifas en **121/121** fechas del horizonte. El commit 9ab3d25 solo CLASIFICÓ ese vacío como `ok:true` (no-falla); la causa de fondo (parse viejo / carga discontinua real) sigue sin diagnosticar. NO se tocó en esta serie de commits | PENDIENTE | — | `competitor_scrape_state`: 121 filas de "Reserva Directo", todas `had_data=false`, rango 2026-07-19..2027-01-10. Próximo paso: volcar la respuesta cruda del endpoint como se hizo con Booking, para separar "no publicado" de "parse desactualizado" |
| Competencia y techo: NUNCA mostrar fixtures como mercado real (§6.4). (1) Sin fallback a `competitor_scraper_results` (fixtures manuales sin fecha, USD 70-150): mostrarlos para una fecha que nadie scrapeó era competencia FALSA (enero mostraba los precios de julio). Las filas no se borran, dejan de alimentar la consulta. (2) `pricing_ceilings` marcado EJEMPLO se ignora: el seed USD 180 se presentaba como techo real. (3) BUG DE FONDO destapado: la query de competencia por fecha pedía `source_url`, columna inexistente en `competitor_prices` → PostgREST 42703 + `data:null` con el error sin leer → la competencia por fecha SIEMPRE quedaba vacía y TODO caía al fallback. La competencia real por fecha nunca se había usado | HECHO | 2026-07-21 | commit 710304b; run 2027-01-10 (lejana): "competencia NO publicada", techo "no configurado", 0 ocurrencias de 130/180. run 2026-07-20 (cercana, PXSOL mapeado): mediana USD 175.44 con nombre+precio+link, confianza high. tsc=0, 121/121 |
| Booking: resolver `comparable_capacity` (§1.6) + primer test de INTEGRACIÓN contra la base real. Corrección al diagnóstico: no era "max_number_of_guests=0" sino que `occupancyOf` leía campos inexistentes — `offers[].occupancy` es OBJETO `{adults,children,total}` (se hacía `Number({...})`=NaN) y `max_number_of_guests` vive en `availability[]` (array hermano de `pricing[]`, nunca consultado). Orden nuevo: occupancy de la oferta → availability[] del mismo room_type → bed_configuration. Sin señal: capacidad null, fila SE GUARDA (precio real) con marca "CAPACIDAD DESCONOCIDA", no se pierde. Test de integración corre las MISMAS queries de producción contra el esquema real (los mocks no cazan columnas inexistentes) | HECHO | 2026-07-21 | commit 0df6f9f; cron `--dates 2026-07-28`: fila de Booking con `comparable_capacity=4`; cotización UF214 (cap 4): "Linda Bay 202 USD 140" entra a la decisión; UF508 (cap 6): §1.6 excluye correctamente. Test verificado con dientes (reintroducir `source_url` → falla 42703). tsc=0, 132/132 |
| Seed baja temporada `per_night` faltante (2026-07-28..2026-08-14): hueco entre "Vacaciones invierno" (package, hasta 27/07) y "Finde largo agosto" (package, 15-17/08) → el resolver daba SIN_DATO para todo el rango, único bloqueo de la demo e2e. Migración versionada (no insert suelto): temporada "Baja invierno 2026" clonando las 4 tarifas de "Base octubre". `property_id` heredado por subquery (nunca hardcodeado). Precio clon = placeholder; el CHECK de `unit_rates.source` (DDL gobernado, enum cerrado) no admite marca custom → clon hereda `inferred_from_history`, la trazabilidad vive en el nombre de la migración y en `rate_seasons.name` | HECHO | 2026-07-21 | migración `20260721142217_seed_baja_invierno_2026_cloned_from_base_octubre`; season_id `b74222b0-8b20-49d3-947b-7d0a80d7454d`; preview+rollback antes de aplicar (atajó el 23514 del CHECK). run 2026-07-28: SIN_DATO eliminado, UF214 MANTENER USD 95.46 confianza high |
| Dos reglas de negocio del run 2026-07-28. FIX 1 (gate de pace): la ocupación baja solo empuja a BAJAR si hay pace válido (`pace.computable`) — sin pace, 1-2 meses por delante en baja/media con pocas reservas NO es demanda débil (el booking window no arrancó), no se recorta la base; el gap sobrevive como acción ACOTADA (gap optimization), nunca como recorte de base. FIX 2 (Explainability, Principio 15): `detectPriceInversions()` — si una unidad de mayor capacidad queda ≤ (tol 5%) que una menor para la misma fecha, se emite justificación OBLIGATORIA nombrando la causa. Cableado en los 2 call sites multi-unidad (script + orchestrator/Telegram) | HECHO | 2026-07-21 | commit 40b29fa; run 2026-07-28: UF508 pasa de BAJAR 96.92 a MANTENER 102.02 (por encima de sus bases), Caja Blanca declara "sin pace... no bajo la base". Inversión probada e2e forzando tol 10% (llega a decision.reasons con 3 causas). tsc=0, 139/139 (7 tests nuevos) |
| Calendario de precios en dashboard | DIFERIDO | — | SPEC-PRICING-MODEL §5, ARCH-0023 |

**Gaps de datos abiertos (reportados, no inventados — DOMAIN_KNOWLEDGE §3.2):**
- 🟢 ~~`reservations` vacía → ocupación y gap nights NO calculables~~ **DESBLOQUEADO** (2026-07-17): 30 reservas cargadas → ocupación real + gap nights YA se calculan y alimentan la decisión. Queda solo: 🟡 **booking pace (§5.2)** no reconstruible hacia atrás (el histórico tiene fecha de estadía, no de reserva); se acumula desde ahora (booking_date en reservas nuevas).
- 🔴 Elasticidad para el revenue simulator (§5.4): sin histórico de elasticidad no se infiere (§5.3). Corre solo con supuesto explícito del propietario. Desbloqueo: histórico de (precio → ocupación lograda) o supuesto declarado.
- 🟡 Competencia sin fecha: los 10 competidores son de 2026-08-04; para decidir dirección se necesita competencia de la fecha consultada (Paso 4).
- 🟡 **Competencia de fechas LEJANAS no disponible todavía** (verificado 2026-07-20, no supuesto): el snapshot crudo de Bright Data para 2027-01-05 (probado a 1 y a 7 noches) devuelve las 2 URLs **sin `error_code`, con `pricing: []` y `availability: []`** — request OK y parseo OK, el competidor simplemente no cargó tarifa. La misma llamada a 2026-07-25 sí trae `pricing` poblado. Es carga discontinua del competidor, no un bug del adapter. Consecuencia: hoy solo hay compset near-term; alta temporada se irá poblando a medida que los competidores publiquen. `Linda Bay 205` está vacío incluso en fechas cercanas (esa unidad no está abierta).
- 🟢 ~~Techo actual es un valor de EJEMPLO (USD 180)~~ **NEUTRALIZADO** (2026-07-21, commit 710304b): la fila marcada EJEMPLO se ignora, ya no se presenta como techo real (se muestra "sin techo real configurado"). El techo SCRAPEADO real (fuente `is_ceiling`) sigue funcionando. Queda 🟡: el propietario debe cargar su techo real (§4) para que haya un techo configurado no-ejemplo.
| AI Orchestrator | DIFERIDO | ADR-010, RFC-0002 / ARCH-0012 |
| Context Builder (context real, no piso mínimo) | DIFERIDO | ADR-010, ARCH-0029 |
| Property Brain / Guest Brain / Growth Brain (memoria vectorial) | DIFERIDO | ADR-010, RFC-0003 / ARCH-0013 |
| Multi-Agent System completo | DIFERIDO | ADR-010, RFC-0004 / ARCH-0014 |
| BDVL como capa formal de validación (hoy solo logging + policy) | DIFERIDO | ADR-010, ARCH-0022 |
| Competitor Scout automático (scraping OTAs + matching + Decision Graph) | DIFERIDO | ADR-015, RFC-0008 |
| Techo de precio automático + señal macro estacional | DIFERIDO | ADR-014 |
| WhatsApp como canal (Telegram es interino) | DIFERIDO | ADR-003-A |
| Direct Booking Engine + Payments (MercadoPago) | DIFERIDO | RFC-0007, ARCH-0019 Etapa 6 |
| Growth Intelligence (generación de contenido, marketing ABC1) | DIFERIDO | RFC-0009 (tablas content_* existen como logging floor, ADR-010-D) |
| Descubribilidad agéntica / MCP tooling | DIFERIDO | ADR-012, ADR-007, RFC-0010 |
| Decision Graph (aprende de aprobaciones/rechazos del dueño) | DIFERIDO | ADR-013, ADR-015 |

---

## FASE 3+ — Visión completa NeuralCore

| Ítem | Estado | Respaldo |
|---|---|---|
| Plataforma multi-módulo AI-native completa (Etapas 4-7 del roadmap) | DIFERIDO | ARCH-0019, RFC-0001..0010 |
| Protocol Rails / ecosistema agéntico | DIFERIDO | ARCH-0019 Etapa 7, RFC-0010 |

---

## Documentos de ingeniería aún no escritos (backlog conocido, no perdido)

| Doc | Estado | Nota |
|---|---|---|
| API Spec (OpenAPI) | PENDIENTE | no bloqueante (CLAUDE.md gaps) |
| Prompt Engineering Standards | PENDIENTE | no bloqueante |
| Coding Standards | PENDIENTE | no bloqueante |
| DOC-34 a DOC-60 (Glossary, Onboarding, Playbook, etc.) | DIFERIDO | backlog iterativo (CHANGELOG_RECONCILIATION §Sesión 2) |

# ESTADO DEL PROYECTO ATLAS — Tablero Único de Verdad

**Última actualización:** 2026-07-15
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
| Paso 4: agendar el cron (GitHub Actions/cron externo) — el worker existe, falta el scheduler | PENDIENTE | — | ARCH-0027; corre por script (Booking poll de minutos), no en el webhook |
| Calendario de precios en dashboard | DIFERIDO | — | SPEC-PRICING-MODEL §5, ARCH-0023 |

**Gaps de datos abiertos (reportados, no inventados — DOMAIN_KNOWLEDGE §3.2):**
- 🟢 ~~`reservations` vacía → ocupación y gap nights NO calculables~~ **DESBLOQUEADO** (2026-07-17): 30 reservas cargadas → ocupación real + gap nights YA se calculan y alimentan la decisión. Queda solo: 🟡 **booking pace (§5.2)** no reconstruible hacia atrás (el histórico tiene fecha de estadía, no de reserva); se acumula desde ahora (booking_date en reservas nuevas).
- 🔴 Elasticidad para el revenue simulator (§5.4): sin histórico de elasticidad no se infiere (§5.3). Corre solo con supuesto explícito del propietario. Desbloqueo: histórico de (precio → ocupación lograda) o supuesto declarado.
- 🟡 Competencia sin fecha: los 10 competidores son de 2026-08-04; para decidir dirección se necesita competencia de la fecha consultada (Paso 4).
- 🟡 Techo actual es un valor de EJEMPLO (USD 180); el propietario debe definir el real (§4).
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

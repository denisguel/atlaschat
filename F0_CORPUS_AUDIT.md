# F0 — Auditoría de Integridad del Corpus

**Fecha:** 2026-07-21
**Alcance:** Entregable 2 de la tarea F0 (Saneamiento del corpus, Plan Maestro v1.0 §7). Triage, no reescritura — ningún doc existente fue editado; ver `ADR/ADR-003-B_*.md` y `ADR/ADR-010-E_*.md` para las dos excepciones autorizadas (ID en encabezado de doc nuevo, no de doc colisionado).
**No se tocó código, DB ni tests.**

---

## 2.1 Inventario y detección de defectos

### Resumen por namespace

| Namespace | Dir | Docs | Rango declarado | IDs colisionados (mismo ID en 2 archivos) |
|---|---|---|---|---|
| FOUNDATION | `FOUNDATION/` | 5 | sin esquema numérico único (AES-00, 01, 02, 2 sin número) | — |
| ADR | `ADR/` | 22 (20 previos + 2 de F0) | 001–015, más letras (003-A/B, 010-B/C/D/E, 011-A) | **0** — todos únicos |
| RFC | `RFC/` | 10 | 0001–0010, sin huecos | **0** |
| ARCH | `ARCH/` | 28 | 000–010 (sin padding) + 0012–0030 (con padding) | **0** |
| DOC | `DOC/` | 5 | 0011, 0017, 0031, 0032, 0033 | **0** |
| Raíz (specs con nombre propio, sin ID formal) | `.` | ~12 | sin esquema — ver §2.1.c | — |

**Conclusión clave:** no hay colisiones de ID *dentro* del corpus existente — la revisión archivo-por-archivo (contenido real del encabezado, no solo nombre de archivo) confirma que cada ID declarado es único. Las únicas colisiones detectadas fueron entre los **IDs pedidos para los 2 ADR nuevos de F0** y ADRs ya Accepted — resueltas en Entregable 1 (ver nota al pie de cada archivo nuevo). Esto contradice la premisa inicial de la tarea ("colisiones de ID" como defecto ya presente) — el defecto real es más angosto: *riesgo* de colisión al registrar decisiones nuevas sin consultar el corpus vigente primero, no colisión ya consumada.

### (a) IDs duplicados/colisionados

Ninguno dentro del corpus preexistente (ver arriba). Los 2 casos detectados fueron entre contenido *nuevo* (pedido por esta tarea) y el corpus — resueltos, no reportados como defecto del corpus en sí:

- "ADR-011 (Telegram-puente)" pedido → colisiona con `ADR-011_CLAUDE_AS_PRIMARY_AI_PROVIDER.md` (Accepted 2026-07-18, citado en CLAUDE.md como decisión cerrada). Registrado como **ADR-003-B** en su lugar.
- "ADR-012 (reducción de alcance Fase 1)" pedido → colisiona con `ADR-012_AGENTIC_DISCOVERABILITY_PHASE2_PRIORITY.md` (Accepted). Registrado como **ADR-010-E** en su lugar.

### (b) Huecos y drift de numeración

1. **ARCH — cambio de esquema sin explicar.** ARCH-000 a ARCH-010 usan 3 dígitos sin padding (`ARCH-002`, `ARCH-003`...); ARCH-0012 en adelante usan 4 dígitos con padding (`ARCH-0012`, `ARCH-0013`...). El corte ocurre exactamente donde el corpus pasa de "specs numeradas 01–20" (esquema legacy, español, "Documento NN") al esquema ARCH-00XX actual. Ningún doc explica el corte; `00_INDICE_MAESTRO.md` lista ambos rangos en la misma tabla sin nota.
2. **ARCH-001, ARCH-011 y ARCH-0017 no existen.** Huecos reales, no error de mi inventario — confirmado revisando `ARCH/` dos veces. No hay evidencia de que hayan sido supersedidos (no aparecen en `_ARCHIVE`); probablemente nunca se escribieron. No bloqueante (INDICE_MAESTRO ya trata varios rangos como "no bloqueante para Phase 1"), pero deja huecos silenciosos en la numeración que un futuro lector puede confundir con archivos perdidos.
3. **ARCH-0019 — drift interno de numeración.** El archivo `ARCH-0019_Product_Roadmap_And_Evolution.md` se titula internamente **"Documento 20"** ("## Documento 20 — Roadmap Tecnológico..."), no "Documento 19". Es decir: el ID de archivo/curación (ARCH-0019) y el número de documento legacy que el propio texto declara (20) no coinciden. `00_INDICE_MAESTRO.md` no lo menciona. No se corrigió el contenido (línea roja: ADRs/specs no se editan) — se deja consignado acá para que el Registry de F1 cite `ARCH-0019` (el ID de archivo, que es el que gobierna) y no se confunda con un "ARCH-0020" inexistente bajo ese nombre.
4. **DOC — namespace disperso por diseño, no por error.** Solo 5 de un rango 0001–0060+ existen (0011, 0017, 0031, 0032, 0033). `00_INDICE_MAESTRO.md` ya documenta esto como decisión editorial explícita ("27 documentos restantes del plan original... se generan cuando el código los necesite"), así que NO se reporta como drift — es sparse-by-design, ya justificado en el corpus.
5. **`DOC-0061` — referenciado por la tarea F0, no existe en el corpus.** Ver §2.2.a — esto bloqueó parcialmente el checklist de contradicciones pedido.

### (c) Docs sin ID formal

Docs canónicos citados por nombre propio, sin esquema ADR/RFC/ARCH/DOC:

| Archivo | Citado como obligatorio en | Nota |
|---|---|---|
| `DOMAIN_KNOWLEDGE.md` | CLAUDE.md §Lectura obligatoria | — |
| `SPEC-PROACTIVE-PRICING-MODEL.md` | CLAUDE.md §Lectura obligatoria | — |
| `manual_revenue_pricing.md` | CLAUDE.md §Lectura obligatoria | — |
| `SPEC-PRICING-MODEL.md` | CLAUDE.md (estructura de datos de tarifas) | Estado propio: "Draft — requiere completar 2 puntos con el propietario" |
| `ATLAS_PLAN_MAESTRO_SAAS.md` | Este mismo F0 (contexto de la tarea) | Sin trackear en git al iniciar F0 (ver §2.4) |
| `00_INDICE_MAESTRO.md` | — | Desactualizado: fecha 2026-07-03, no incluye ADR-012 a 015, DOC-0032/0033, ni ARCH-0030. `docs/INDEX.md` (Entregable 3) lo reemplaza como fuente de citación vigente — no se borra (línea roja), pero deja de ser autoritativo. |
| `ONBOARDING-COMPETITOR-SOURCES.md`, `GAP_ANALYSIS.md`, `ATLAS_MOAT_AGENTICO_TESIS.md`, `BUSINESS_CASE_AND_FINANCIAL_MODEL.md`, `MASTER_PLAN_CURADO_POST_HOSPIOS.md`, `CHANGELOG_RECONCILIATION.md`, `ESTADO_PROYECTO.md` | — | Ver clasificación individual en `docs/INDEX.md` |

**Hazard adicional (no es un "doc sin ID", es peor — dos docs con el mismo título implícito):** `DOMAIN_KNOWLEDGE.md` y `DOMAIN_KNOWLEDGE old.md` coexisten en la raíz. Ambos declaran el mismo título y la misma línea de estado ("Fecha 2026-07-11, Estado: Canonical"). `DOMAIN_KNOWLEDGE old.md` no está trackeado por ningún doc ni referenciado desde CLAUDE.md — parece un backup manual dejado en el working tree. **No se movió ni se borró** (fuera de los 3 entregables de F0, y el archivo puede ser trabajo en progreso de otra sesión — regla de precaución del harness). Recomendación para Denis: si es efectivamente un backup obsoleto, archivarlo en `_ARCHIVE/` en un pase posterior; si tiene contenido divergente valioso, reconciliar antes de que F1 cite `DOMAIN_KNOWLEDGE.md` a ciegas.

---

## 2.2 Contradicciones — los 6 docs que gobiernan F1 + 2 ADRs nuevos

### (a) Bloqueo parcial: `DOC-0061` no existe

La tarea F0 especificó 6 docs gobernantes: `RFC-0008, ADR-003, ADR-005, ARCH-0022, ARCH-0024, DOC-0061`. Los primeros 5 existen y fueron revisados. **`DOC-0061` no existe en ningún directorio del corpus** (confirmado por búsqueda de texto completo, no solo por archivo faltante). El namespace `DOC` solo llega hasta `DOC-0033`.

**No sustituí silenciosamente otro doc en su lugar.** Propuesta para Denis (no aplicada): el candidato más probable por relevancia temática a F1 (RLS, multi-tenancy, aislamiento por tenant — el eje que ya causó un bug de fuga de datos según DOC-0031 mismo) es **DOC-0031 (Security and Multi-Tenancy Handbook)**. Como verificación de buena fe, ya lo crucé contra los otros 7 docs de este checklist — sin encontrar contradicción (ver ítems abajo) — pero esto necesita tu confirmación antes de tratarlo como parte oficial del set gobernante en `docs/INDEX.md`.

### (b) Contradicciones encontradas

**CONTRADICCIÓN 1 — vigencia de ADR-005 sin nota de fase.**
`ADR-005` (Brain Memory Architecture, Accepted 2026-06-29) §status dice `Accepted`, sin ninguna nota de que la arquitectura de memoria persistente (Property Brain / Guest Brain / Growth Brain) esté diferida. / `ADR-010` (Phase 1 Scope Reduction, Accepted 2026-07-03, **posterior**) §Decision dice explícitamente: *"Explícitamente fuera de Phase 1: Property Brain, Guest Brain, Growth Brain (RFC-0003 / ARCH-0013)"*.

No es que los dos documentos afirmen hechos incompatibles sobre el mismo estado — es que `ADR-005` nunca fue actualizado para reflejar que quedó pausado, y "Accepted" sin calificar puede leerse como "vigente ahora". Un extractor de reglas de F1 que cite `ADR-005` sin cruzar con `ADR-010` extraería reglas de memoria como si gobernaran Fase 1.

**Propuesta de resolución:** `ADR-010` gana para alcance de F1 (ya es la decisión de mayor jerarquía temporal y la única con scope explícito de fase — además CLAUDE.md ya la trata como cerrada). En `docs/INDEX.md`, `ADR-005` queda marcado `VIGENTE (gobierna Fase 2+, diferido de facto por ADR-010 para F1)`, `gobierna F1: NO`. No se edita `ADR-005` (línea roja de inmutabilidad) — la resolución vive en el índice, no en el doc.

**CONTRADICCIÓN 2 — ninguna encontrada entre los 5 docs restantes.**
Verificación explícita realizada, no asumida:
- `RFC-0008` (§34, línea 863) y `ARCH-0022` (§12, línea 329) mencionan "WhatsApp" como canal en sus diagramas — ambos son docs de visión de arquitectura completa, escritos antes de que `ADR-003-A` formalizara Telegram como interino. **No es contradicción** con `ADR-003`/`ADR-003-A`/`ADR-003-B`: esos tres ya reconcilian explícitamente "WhatsApp es destino, Telegram es el canal real de Phase 1" — RFC-0008/ARCH-0022 simplemente no fueron actualizados para nombrar el canal actual, lo cual es visión-nivel-esperado, no un choque de hechos. **Nota de citación, no contradicción:** en `docs/INDEX.md` se aclara que las menciones a "WhatsApp" en RFC-0008/ARCH-0022 son de canal-objetivo, no una afirmación de que Telegram esté fuera de spec.
- `ARCH-0024` (§4, "Definition of Truth") lista `Prices` entre las entidades cuyo SSOT es PostgreSQL — consistente con `price_calendar` (SPEC-PROACTIVE-PRICING-MODEL.md) como tabla SSOT de tarifas vigentes. Sin contradicción.
- `RFC-0008` (línea 622: *"Opportunity detection operates continuously rather than only when the owner requests an analysis"*) exige el modelo PROACTIVO — exactamente lo que `SPEC-PROACTIVE-PRICING-MODEL.md` implementa como "el CÓMO operativo de RFC-0008" (su propio subtítulo lo dice). Consistentes, no es una contradicción del corpus — es el bug de CÓDIGO (bot reactivo) que ese SPEC ya documenta haber corregido, no un conflicto entre los docs.
- `ARCH-0022` (§13, "High Risk") clasifica "Price adjustment" como riesgo alto que requiere validación de política — consistente con el flujo de aprobación de `SPEC-PROACTIVE-PRICING-MODEL.md` ("el propietario aprueba/rechaza → la tabla se actualiza").
- `DOC-0031` (candidato a DOC-0061, ver 2.2.a) — sin contradicción encontrada contra ninguno de los 5 anteriores ni contra los 2 ADR nuevos.

### (c) Los 2 ADR nuevos vs. el resto del set

Ya cubierto en detalle en Entregable 1: `ADR-003-B` no contradice `ADR-003`/`ADR-003-A`, los completa (resuelve su ítem "A revisar" pendiente). `ADR-010-E` no contradice `ADR-010`, lo reafirma sin reabrirlo. Ninguno de los dos contradice `RFC-0008`, `ARCH-0022`, `ARCH-0024`, ni (verificado) `DOC-0031`.

---

## 2.3 Propuesta de marcado REFERENCIA

Criterio aplicado: *¿alguna decisión de código de F1–F5 (según `ATLAS_PLAN_MAESTRO_SAAS.md` §7) depende de este doc hoy?* Si no → REFERENCIA. Esto es una **propuesta** — no se movieron archivos ni se cambió ningún encabezado; el estado final vive en `docs/INDEX.md` (Entregable 3), editable sin tocar el doc en sí.

**Propuesto REFERENCIA (visión completa / fases F6+, no gobierna F1–F5):**
- `RFC-0002` (AI Orchestration), `RFC-0003` (Memory System), `RFC-0004` (Multi-Agent System) — los tres explícitamente diferidos por `ADR-010`.
- `RFC-0007` (Direct Booking Engine) — Plan Maestro lo ubica en F8.
- `RFC-0009` (Growth Intelligence System) — Plan Maestro lo ubica en F9; el propio texto de la tarea F0 lo cita como ejemplo de REFERENCIA.
- `ARCH-0012` (AI Orchestration Implementation), `ARCH-0013` (Memory Architecture), `ARCH-0014` (Multi-Agent System Implementation) — implementación de lo diferido arriba.
- `ARCH-0018` (MVP Implementation Roadmap), `ARCH-0019` (Product Roadmap), `ARCH-0020` (Master System Specification v2) — roadmaps de visión, probablemente superseded en la práctica por `ATLAS_PLAN_MAESTRO_SAAS.md` §7 (fases F0–F9) aunque nadie lo declaró formalmente — **flagueado, no resuelto** (ver 2.4).
- `MASTER_PLAN_CURADO_POST_HOSPIOS.md` — roadmap anterior (Fase 0-1-2-3 por meses) que `ATLAS_PLAN_MAESTRO_SAAS.md` (F0-F9 por días/semanas) parece haber reemplazado en la práctica. Mismo flag que el ítem anterior.

**Ambiguos — necesitan tu criterio, no los until asumo:**
- `ARCH-0029` (Context Retrieval Strategy) — si describe la estrategia completa de Context Builder (diferido por ADR-010), es REFERENCIA; si describe algo que `ADR-010-B` (piso de contexto mínimo) ya usa parcialmente hoy, gobierna. No alcancé a leerlo en profundidad esta pasada — lo dejo `gobierna: A CONFIRMAR` en el índice en vez de adivinar.
- `RFC-0005` (Workflow Orchestration) y `ARCH-0026` (Workflow Responsibility Model) — `ADR-006` eligió n8n como engine, pero el código real de `atlas-mvp` que vengo tocando esta sesión usa cron de GitHub Actions y scripts `.mts`, no workflows de n8n. Si n8n no está efectivamente wireado, estos dos docs no gobiernan ninguna decisión de código *actual*, aunque la decisión arquitectónica (ADR-006) siga Accepted. Marcado `A CONFIRMAR`, no until asumido REFERENCIA.

**NO propuesto REFERENCIA (gobierna, verificado contra código/CLAUDE.md de esta sesión):**
`ADR-003/003-A/003-B`, `ADR-010` + enmiendas, `ADR-011/011-A`, `ADR-014`, `ADR-015`, `ARCH-0021`, `ARCH-0022`, `ARCH-0024`, `ARCH-0027`, `ARCH-0028`, `ARCH-0030`, `DOC-0031`, `DOC-0032`, `DOC-0033`, `RFC-0006`, `RFC-0008`, `RFC-0010`, `DOMAIN_KNOWLEDGE.md`, `SPEC-PROACTIVE-PRICING-MODEL.md`, `manual_revenue_pricing.md`, `SPEC-PRICING-MODEL.md`, FOUNDATION (5) — todos citados o consistentes con decisiones ya implementadas en `atlas-mvp` esta sesión o en `CLAUDE.md`.

---

## 2.4 Hallazgos fuera de los 3 entregables (reportados, no actuados)

1. **`ATLAS_PLAN_MAESTRO_SAAS.md` cita "ADR-011" 5 veces** (líneas 7, 143, 229, 231, 248) para la decisión que este F0 registró como `ADR-003-B`. Queda desactualizado. No se editó (no es uno de los 3 entregables).
2. **`ATLAS_PLAN_MAESTRO_SAAS.md` línea 248** afirma que la reducción de alcance a Fase 1 está "hoy sin documentar" — inexacto, `ADR-010` la documenta desde 2026-07-03. No se editó.
3. **Posible doble roadmap sin resolver:** `MASTER_PLAN_CURADO_POST_HOSPIOS.md` (2026-07-03) y `ATLAS_PLAN_MAESTRO_SAAS.md` (2026-07-21) parecen describir el mismo tipo de plan de fases con esquemas de fase incompatibles (Fase 0-3 por meses vs. F0-F9 por días/semanas). Ninguno declara al otro superseded. No asumí cuál manda — queda en `docs/INDEX.md` como `A CONFIRMAR`.
4. **`00_INDICE_MAESTRO.md` quedó desactualizado** (fecha 2026-07-03; faltan ADR-012 a 015, DOC-0032/0033, ARCH-0030 en sus tablas). `docs/INDEX.md` lo reemplaza como fuente de citación para F1, pero `00_INDICE_MAESTRO.md` sigue en el repo sin marca de "superseded" — recomendado agregarla en un pase posterior.

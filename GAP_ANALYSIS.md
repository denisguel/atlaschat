# GAP ANALYSIS — Visión Completa vs. Corpus Curado vs. Base Real

**Fecha:** 2026-07-11
**Alcance:** cobertura de entidades y features de la VISIÓN COMPLETA (todas las fases), no solo Fase 1.
**Regla:** no se afirma cobertura sin verificar contra fuente real. Lo perdido se dice explícito.

## Fuentes comparadas (verificadas)

| Fuente | Qué es | Verificación |
|---|---|---|
| `_ARCHIVE/legacy_flat_structure/` | 60 docs originales | `ls` → 60 archivos (+ dir `versiones_previas_duplicado`) |
| Corpus curado (`ADR/ARCH/DOC/RFC/FOUNDATION/`) | Canónico reconciliado | 20 ADR · 28 ARCH · 5 DOC · 10 RFC · 5 FOUNDATION |
| `CHANGELOG_RECONCILIATION.md` | Registro editorial de la curación | Leído íntegro (2 sesiones documentadas) |
| DOC-0032 | Data Model Spec Fase 1 | 12 tablas especificadas |
| **Supabase real** (`list_tables`, project `xlhafthdfbrisvxbowjt`) | Tablas efectivamente desplegadas | 13 tablas, todas con RLS |

### Verificación Supabase (list_tables, 2026-07-11)

13 tablas, **todas con `rls_enabled: true`**:
`organizations, properties, units, guests, reservations, pricing_history,`
`competitor_scraper_results, bdvl_decisions, content_assets, content_performance,`
`property_integrations, execution_logs, organization_members`

- ✅ **`competitor_scraper_results` EXISTE** (10 filas, RLS activo, comment de multi-tenancy presente). Verificado, no asumido.
- ✅ DOC-0032 especifica 12 tablas → las 12 están desplegadas.
- ➕ `organization_members` (real, no en DOC-0032): agregada en la construcción para membership/auth + Custom Access Token Hook. **Adición justificada**, no gap.
- ➕ `competitor_scraper_results` tiene 2 columnas más que DOC-0032 (`applies_to_unit_id`, `adjustment_note`): mejora Fase 1 (comparabilidad manual, ADR-015). **Extensión registrada.**

---

## 1. Cobertura de ENTIDADES del domain model (ARCH-004)

El domain model canónico define 7 entidades core. Estado real:

| Entidad (ARCH-004) | En Fase 1 (tabla real) | Estado | Registro |
|---|---|---|---|
| **Property** | `properties` + `units` | ✅ CUBIERTA | DOC-0032 |
| **Guest** | `guests` (perfil simple) | ✅ CUBIERTA (Guest Brain vectorial diferido) | DOC-0032, ADR-010 |
| **Reservation** | `reservations` | ✅ CUBIERTA | DOC-0032 |
| **Message** | — (sin tabla) | ⚠️ **NO PERSISTIDA** | **sin registro explícito — ver Hallazgo #1** |
| **Payment** | — (sin tabla) | 🟡 DIFERIDA | RFC-0007 / ARCH-0019 Etapa 6 (Booking Engine) |
| **Workflow** | — (sin tabla, sin n8n) | ⚠️ DIFERIDA + divergencia | ADR-006 vs realidad — **ver Hallazgo #2** |
| **Event** | — (sin tabla de eventos) | 🟡 DIFERIDA ("event-driven ready") | ADR-002, ADR-009 |

Entidades de soporte Fase 1 que NO están en el domain model pero sí en la realidad (correcto — son piso de Fase 1): `pricing_history`, `competitor_scraper_results`, `bdvl_decisions`, `content_assets`, `content_performance`, `property_integrations`, `execution_logs`, `organization_members`.

---

## 2. Cobertura de MÓDULOS/FEATURES de la visión (RFC-0001..0010)

| Módulo (RFC) | Estado Fase 1 | Registro del diferimiento |
|---|---|---|
| Revenue Intelligence (RFC-0008) | ✅ PARCIAL: Revenue Agent activo (pricing con histórico + competidores manuales) | ADR-010, ADR-010-B |
| Competitor Scout automático (RFC-0008) | 🟡 DIFERIDO (carga manual en Fase 1) | ADR-015 ✅ |
| AI Orchestration (RFC-0002) | 🟡 DIFERIDO (piso de contexto mínimo en su lugar) | ADR-010, ADR-010-B ✅ |
| Memory System / Brains (RFC-0003) | 🟡 DIFERIDO (guests = perfil simple) | ADR-010, DOC-0032 ✅ |
| Multi-Agent System (RFC-0004) | 🟡 DIFERIDO (solo Revenue Agent) | ADR-010 ✅ |
| Workflow Orchestration (RFC-0005) | ⚠️ DIVERGENCIA (ver Hallazgo #2) | ADR-006 — sin ADR de divergencia |
| Hospitality Domain (RFC-0006) | ✅ PARCIAL (Property/Unit/Guest/Reservation) | DOC-0032 |
| Direct Booking Engine (RFC-0007) | 🟡 DIFERIDO (incluye Payments) | ARCH-0019 Etapa 6 ✅ (no hay ADR dedicado) |
| Growth Intelligence (RFC-0009) | 🟡 DIFERIDO (tablas content_* como logging floor) | ADR-010-D ✅ |
| AI Tooling / MCP (RFC-0010) | 🟡 DIFERIDO | ADR-007, ADR-012 ✅ |
| AI-Native Architecture (RFC-0001) | ✅ marco vigente | — |

**Leyenda:** ✅ cubierto o diferido con registro claro · 🟡 diferido con registro adecuado · ⚠️ requiere atención (ver Hallazgos).

---

## 3. Reconciliación documental: ¿se perdió contenido?

El `CHANGELOG_RECONCILIATION.md` documenta la curación con rigor (69 origen → 53 canónicos + 17 archivados). Trazabilidad verificada:

- **Renumeraciones/merges documentados:** ARCH-0028 (legacy "AI Model Routing") → ARCH-0030; fixes de IDs internos (13/14/15/16/18); merge selectivo de contenido de Memory (Decision Graph, Retention) a ARCH-0013; roadmap promovido a ARCH-0019. **Todos con registro.**
- **`03_ULTIMO MD CORREGIR NOMBRE.md`** (borrador precursor, 715 líneas): resuelto en Sesión 2 — promovido a ARCH-003. **Registrado.**
- **Docs nuevos generados (gaps cerrados):** ARCH-0028 (AI Safety/Guardrails), ARCH-0020 v2, ADR-010, ADR-011, ADR-003-A, DOC-0031. **Registrados.**
- **Gap conocido y declarado:** DOC-34 a DOC-60 (≈27 docs de ingeniería) nunca escritos — **decisión editorial explícita**, no pérdida silenciosa.

**Conclusión de nivel documental:** no se detecta contenido de la visión *perdido sin registro* en la reconciliación. El changelog explica cada movimiento. La visión completa (NeuralCore) se preserva íntegra en el corpus curado + `_ARCHIVE`.

---

## 4. HALLAZGOS — lo que se perdió o diverge SIN registro explícito

Estos son los únicos gaps reales (no en la curación de docs, sino entre **visión ↔ implementación Fase 1**) que **no** tienen un ADR/DOC que los registre:

### Hallazgo #1 — Entidad `Message` sin persistencia ni registro de diferimiento
- **Evidencia:** ARCH-004 §4.4 define `Message` como entidad core (id, sender, receiver, channel, content, intent, status). No existe tabla `messages` en DOC-0032 ni en Supabase (`list_tables` verificado). El mensaje se procesa transitoriamente (Channel → routeInbound) y se descarta.
- **Por qué importa:** sin persistir mensajes no hay materia prima para Conversation Memory (RFC-0003) ni para el análisis de intención histórico. ADR-010 **no** lista `Message` entre lo diferido (enumera Orchestrator/Context Builder/Brains/Multi-Agent, no Message/Payment/Workflow/Event).
- **Severidad:** media. No bloquea Fase 1 (el flujo es comando→acción), pero es un diferimiento **implícito no documentado**.
- **Acción sugerida:** un ADR corto que declare explícitamente "Message/Event/Payment/Workflow no se persisten en Fase 1; se difieren a Fase 2" — cierra el hueco de registro sin construir nada.

### Hallazgo #2 — Divergencia n8n no formalizada
- **Evidencia:** ADR-006 adopta n8n como "primary workflow orchestration engine **for the MVP and early production phases**". El código Fase 1 **no tiene n8n** (`grep -ri n8n lib/ app/ package.json` → vacío); se usa Next.js API routes + webhook de Vercel + código directo.
- **Por qué importa:** es exactamente el mismo tipo de divergencia que WhatsApp→Telegram, que SÍ se formalizó (ADR-003-A). Acá no hay un "ADR-006-A" que registre que Fase 1 corre sin n8n.
- **Severidad:** media. La decisión es correcta (n8n sería over-engineering para Fase 1), pero está **sin registrar**, y un colaborador que lea ADR-006 asumiría que hay n8n.
- **Acción sugerida:** ADR-006-A (o nota en ADR-013) que formalice "Fase 1 sin n8n; orquestación por código/serverless; n8n se reevalúa en Fase 2".

### Hallazgo #3 — Drift de scope entre ADR-010 y ADR-015 (menor, ya mitigado)
- **Evidencia:** ADR-010 lista "Scraper de precios de competidores" DENTRO de Fase 1. ADR-015 lo difiere a Fase 2 (Fase 1 usa carga manual).
- **Severidad:** baja. **Ya registrado** vía ADR-015, pero ADR-010 quedó desactualizado (dice que el scraper es Fase 1).
- **Acción sugerida:** nota en ADR-010 apuntando a ADR-015 como supersede parcial. (Registrado acá; no es pérdida silenciosa.)

---

## 5. Resumen ejecutivo

| Dimensión | Veredicto |
|---|---|
| Entidades core (7) | 3 cubiertas, 4 sin tabla (1 sin registro: **Message**) |
| Tablas DOC-0032 (12) | **12/12 desplegadas y verificadas** en Supabase |
| `competitor_scraper_results` existe | ✅ **Sí, verificado** (10 filas, RLS) |
| Módulos de visión (RFC) | Todos cubiertos o diferidos **con** registro, salvo Workflow/n8n (Hallazgo #2) |
| Contenido perdido en la reconciliación de docs | **Ninguno sin registro** — el changelog es trazable |
| Gaps sin registro (visión↔implementación) | **2** (Message sin persistencia; divergencia n8n) + 1 drift menor ya mitigado |

**Lo perdido, dicho explícito:** no hay pérdida de *documentación de visión*. Los únicos huecos son de **registro de diferimiento** en la implementación: la entidad Message (y por extensión Event/Payment/Workflow) se difirió sin un ADR que lo diga, y la salida de n8n no se formalizó. Ambos se cierran con 1-2 ADRs cortos, sin construir nada nuevo.

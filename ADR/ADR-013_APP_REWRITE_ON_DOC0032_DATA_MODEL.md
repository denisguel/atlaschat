# ADR-013: Reescritura de la App sobre el Data Model de DOC-0032 y Descarte de la App Pre-Suscripción

**Status:** Accepted
**Date:** 2026-07-04
**Deciders:** Denis Gueler (founder)
**Relates to:** ADR-010 (Phase 1 Scope Reduction), ADR-011 (Claude as Primary AI Provider), DOC-0032 (Data Model Specification), DOC-0031 (Security & Multi-Tenancy)
**Supersedes:** la app Next.js previa en `atlas-mvp` (rama `main`, commits hasta `901eb02`)

## Context

El repo `github.com/denisguel/atlas-mvp` contenía una app Next.js funcional ("ATLAS Fase 1", commit `99eadc7` en adelante) construida **antes** de que existiera la suscripción de Claude y antes de que se cerrara el corpus documental del 2026-07-03. Al preparar las migraciones de DOC-0032 sobre el proyecto Supabase nuevo (`atlas-fase1`, ref `xlhafthdfbrisvxbowjt`), se detectaron dos divergencias estructurales entre esa app y las decisiones ya cerradas:

1. **Proveedor de IA:** la app usa Gemini para extracción/scraping de precios (commits `f2f8a15` "Aumentar ventana de contenido para Gemini", `901eb02` "Extraccion de precio por regex"). ADR-011 fija **Claude** como proveedor primario para L2-L4 y modelo gratuito solo en L1 con abstracción de proveedor (ADR-008) — no Gemini hardcodeado.
2. **Schema de datos:** la app persiste contra `supabase-schema.sql` + `supabase-patch-v2/v3.sql`, un modelo divergente de DOC-0032 (11 tablas + tenant raíz, RLS obligatorio por DOC-0031). No comparte nomenclatura, aislamiento multi-tenant ni el piso de contexto de ADR-010-B/C/D.

Esa app corresponde a lo que ADR-010/011 llaman implícitamente el estadio **"Fase 1 pre-suscripción"**: un prototipo previo a las decisiones vigentes, no un incremento sobre ellas. Reconciliarla (adaptar su schema y reemplazar Gemini in situ) implicaría arrastrar deuda de arquitectura de un código que precede a todo el corpus canónico.

## Decision

1. **Reescritura, no reconciliación.** La app Next.js previa se **descarta** como base de código. La nueva app se construye sobre el data model de DOC-0032 ya migrado en `atlas-fase1`, respetando RLS (DOC-0031), routing por Execution Level con Claude (ADR-011) y los pisos de ADR-010-B/C/D.

2. **`feat/data-model-migrations` es la base de la reescritura.** Esa rama (commit `a3cf920`: 13 migraciones + Custom Access Token Hook + `config.toml`) pasa a ser el punto de partida del nuevo código, no un feature branch a mergear-y-olvidar. La app previa en `main` **no se borra todavía** — se preserva por trazabilidad hasta que la reescritura tenga paridad funcional mínima, momento en que se define en un ADR/acción posterior cómo se retira (borrado, archivado o `_ARCHIVE`).

3. **Razón formal del descarte:** la app previa (a) usa Gemini contra ADR-011, y (b) persiste contra un schema divergente de DOC-0032 sin el modelo de aislamiento multi-tenant de DOC-0031 — las dos causas raíz que hacen más barata la reescritura que la migración.

## Options Considered

### Option A: Reconciliar la app previa (adaptar schema + reemplazar Gemini in situ)
| Dimensión | Evaluación |
|---|---|
| Costo inicial | Aparentemente menor (se reusa UI/rutas existentes) |
| Deuda arrastrada | Alta — el schema divergente y los supuestos de Gemini están entrelazados con la lógica; migrar tabla por tabla arriesga replicar el bug de multi-tenancy que DOC-0031 existe para prevenir |
| Consistencia con corpus | Baja — parte de código que precede a ADR-010/011 |

### Option B: Reescritura sobre DOC-0032 con `feat/data-model-migrations` como base (elegida)
| Dimensión | Evaluación |
|---|---|
| Costo inicial | Mayor (se rehace la capa de app) |
| Deuda arrastrada | Nula — arranca alineada a RLS, abstracción de proveedor y pisos de Fase 1 |
| Consistencia con corpus | Total — el data model ya está migrado y verificado (DOC-0033 #1/#2) |

### Option C: Empezar un repo nuevo desde cero
| Dimensión | Evaluación |
|---|---|
| Costo inicial | Mayor + pérdida de historia de infra ya versionada (migraciones, config del hook) |
| Trazabilidad | Peor — rompe el vínculo con las migraciones ya pusheadas y verificadas |
| Beneficio marginal | Nulo respecto a Option B, que ya arranca limpia sobre la rama de migraciones |

## Trade-off Analysis

Option B acepta un costo inicial mayor a cambio de eliminar deuda de arquitectura en la capa más sensible del sistema (aislamiento multi-tenant, donde ya hubo un incidente real — DOC-0031 §2). Option A parece más barata pero optimiza el corto plazo replicando supuestos que ADR-010/011 ya reemplazaron; el riesgo de reintroducir fuga de datos entre tenants al portar un schema que nunca tuvo RLS supera el ahorro. Option C descarta trabajo de infra ya verificado sin ganar nada sobre B.

## Consequences

**Facilita:**
- App alineada desde la primera línea a DOC-0032/DOC-0031 y a la abstracción de proveedor de ADR-011 (sin Gemini hardcodeado)
- Base de código sobre migraciones ya verificadas (aislamiento lectura/escritura entre 2 tenants + hook de claim, DOC-0033 #1/#2)

**Dificulta:**
- Se rehace la capa de app previa — no hay reuso directo del código de `main`
- Coexisten temporalmente dos estados en el repo (`main` con la app vieja, `feat/data-model-migrations` con la nueva base) hasta el retiro formal de la primera

**A revisar:**
- Momento y mecanismo de retiro de la app previa (borrado vs `_ARCHIVE`) — se decide cuando la reescritura alcance paridad funcional mínima, no ahora
- Qué elementos de UI/UX de la app previa (si alguno) vale la pena reimplementar sobre el nuevo data model — evaluación de producto, no de arquitectura

## Action Items
1. [ ] Esperar OK del founder al plan de reescritura antes de escribir código de app (bloqueante explícito de esta sesión)
2. [ ] NO mergear `feat/data-model-migrations` a `main` ni borrar la app previa hasta ese OK
3. [ ] Activar el Custom Access Token Hook en el proyecto remoto (`supabase config push` o dashboard) antes de que la app nueva dependa de RLS end-to-end
4. [ ] Al alcanzar paridad mínima, abrir ADR/acción de retiro formal de la app pre-suscripción

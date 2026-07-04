# CHANGELOG — Reconciliación de Documentación ATLAS v2.0

Auditoría forense ejecutada sobre repo `denisguel/atlaschat` (69 archivos origen → 53 canónicos + 17 archivados).

---

## Metodología

1. Clonado el repo completo, inspeccionado cada archivo (frontmatter, headers, contenido, IDs internos).
2. Localizado `ATLAS_Documentation_Revision_Plan_v2.md` — plan maestro que define qué documento reemplaza a cuál. Usado como fuente de autoridad primaria.
3. Comparado contra Project Knowledge ya cargado (60 docs con nombres normalizados) para detectar desincronización.
4. Resueltas todas las colisiones de ID y contenido cruzado con evidencia, no con suposición.

---

## Decisiones mecánicas (alta confianza, ejecutadas sin ambigüedad)

| # | Acción | Justificación |
|---|---|---|
| 1 | Normalización de 100% de los nombres de archivo (sin espacios, sin `&`, sin mayúsculas inconsistentes) | Rompían scripts, CI/CD, git sin comillas |
| 2 | Purga de `New Text Document.TXT` | Archivo vacío, artefacto de Windows |
| 3 | Purga de carpeta `versiones previas/` del árbol activo → movida a `_ARCHIVE/versiones_previas/` | Historial no debe vivir en main; para eso existe git |
| 4 | Fix ID interno: `13_MEMORY_ARCHITECTURE` declaraba `ARCH-0014` → corregido a `ARCH-0013` | Colisión con Multi-Agent System, ya detectada en auditoría previa |
| 5 | Fix ID interno: `14_MULTI_AGENT_SYSTEM` declaraba `DOC-0014` → corregido a `ARCH-0014` | El plan maestro define explícitamente "ARCH-0014_MULTI_AGENT_SYSTEM_IMPLEMENTATION_v2 → reemplaza ARCH-0014" |
| 6 | Fix ID interno: docs 15, 16, 18 declaraban prefijo `DOC-00NN` → corregidos a `ARCH-00NN` | El plan maestro lista explícitamente estos tres como reemplazos de `ARCH-0015/0016/0018` |
| 7 | Renombrado `ARCH-0028 AI Model Routing & Provider Strategy.md` → `ARCH-0030_AI_Model_Routing_And_Provider_Strategy.md` (incluye fix del ID interno) | El plan maestro asigna explícitamente "AI Model Routing" al slot **ARCH-0030**, no ARCH-0028. Confirma el defecto ya registrado ("ARCH-0028/0030 numbering drift") con causa raíz exacta |

## Decisiones de juicio (ejecutadas con recomendación — revisables)

| # | Conflicto encontrado | Resolución aplicada | Alternativa si no estás de acuerdo |
|---|---|---|---|
| 8 | `19_PRODUCT_ROADMAP_AND_EVOLUTION.md` (v1, inglés, stages 1-5) **vs** `20_MASTER_SYSTEM_SPECIFICATION2-0.md` (contenido real: roadmap en español, "Etapas 0-3", estilo "Aprobado por el Panel") — este último estaba **mal etiquetado como doc 20** cuando su tema real es Roadmap | Promovido el contenido de `20_..._2-0.md` a `ARCH-0019_Product_Roadmap_And_Evolution.md` (canónico, más reciente, revisado por panel). El v1 en inglés se archivó como superseded. | Si el v1 en inglés tiene contenido que preferís mantener, decime y hago merge selectivo en vez de reemplazo total |
| 9 | `19_PRODUCT_ROADMAP_AND_EVOLUTION2-0.md` — su contenido real es **Arquitectura de Datos y Memory System** (5 Memorias: Conversation, Guest Brain, Property Brain, Growth Brain), no roadmap | Archivado como `DUPLICATE_Memory_Content_mislabeled_as_Doc19...`. No se fusionó automáticamente con `ARCH-0013_Memory_Architecture.md` porque requiere comparación línea por línea para evitar pérdida o contradicción de contenido | Pedime el diff detallado contra ARCH-0013 si querés que decida qué mergear |
| 10 | `03_ULTIMO MD CORREGIR NOMBRE.md` (715 líneas) — nombre literal "último, falta corregir nombre", nunca integrado | **No** se promovió a v2 de ARCH-003 (System Components). Su contenido real (Execution Levels L0-L4, BDVL, Context Builder, Cost Control) es un **borrador precursor** que luego se descompuso en ARCH-0021, ARCH-0022, ARCH-0027, ARCH-0029 por separado. Archivado como referencia histórica. | Si creés que tiene detalle no capturado en los ARCH-002x actuales, puedo hacer un diff de contenido |
| 11 | `02_HIGH_LEVEL_ARCHITECTURE.md` (ARCH-002) y `02_MVP_SCOPE.md` comparten el prefijo "02" pero son de dos sistemas de numeración paralelos (catálogo ARCH-00N vs capítulos de Master Specification) | Mantenidos ambos sin renumerar — no colisionan formalmente (solo uno declara ID ARCH-00N) | Cosmético, baja prioridad — se puede resolver con prefijo `MSPEC-` si preferís cero ambigüedad visual |

## Gaps confirmados (no inventados — documentos que genuinamente no existen)

| Doc | Estado |
|---|---|
| **ARCH-0028** (AI Safety and Guardrails) | Nunca fue escrito en ningún lote de generación. No fabriqué contenido — requiere diseño real de guardrails con tu input |
| **ARCH-0020 v2** (Master System Specification v2.0) | El plan maestro la pide; nunca se generó (lo que existía en ese slot era en realidad el roadmap, reubicado en punto 8) |
| **ADR-010** | Scope reduction a Phase 1 nunca formalizada — ya identificado en auditoría previa a esta sesión |
| **DOC-31 a DOC-60** | 30 documentos de ingeniería listados en el plan maestro (Data Model Spec, API Spec OpenAPI, Security Handbook, Testing Strategy, Coding Standards, etc.) — ninguno fue generado aún |

## No tocado / fuera de alcance de esta pasada

- Contenido interno de cada documento (solo se corrigieron metadatos/IDs/ubicación, no se reescribió prosa)

---

## SESIÓN 2 — Resolución de puntos de juicio + documentación de cierre

### Puntos de juicio resueltos (con decisión tomada, no solo flagged)

| # | Conflicto | Resolución ejecutada |
|---|---|---|
| 1 | ARCH-0019 v1 (inglés) vs contenido promovido (español) | **Merge, no reemplazo total.** Addendum con secciones estratégicas de v1 (Platform Moats, Success Metrics, Strategic Constraints, Future Vision, Ecosystem Expansion, MCP Evolution) agregado al final. v1 completo archivado. |
| 2 | Contenido de Memoria duplicado (mal etiquetado "doc 19") vs ARCH-0013 | **Merge selectivo.** Extraídas solo las 2 secciones con valor no cubierto (Decision Graph, Política de Retención) como addendum v2.1 de ARCH-0013. Resto archivado por redundar con ARCH-0029. |
| 3 | `03_ULTIMO MD CORREGIR NOMBRE.md` | **Confirmado por el usuario como reemplazo real.** Promovido a `ARCH-003_System_Components_And_Runtime.md` (canónico). v1 anterior archivada. |

### Documentos nuevos generados (gaps reales cerrados, cero contenido inventado sin base)

| Documento | Por qué se escribió |
|---|---|
| **ARCH-0028** (AI Safety and Guardrails) | Gap crítico — nunca existió. Modelo de amenazas de IA, guardrails por Execution Level, aislamiento multi-tenant a nivel de IA, guardrails por proveedor |
| **ARCH-0020 v2** (Master System Specification) | v1 obsoleta tras ARCH-0021-0030. Sintetiza el corpus completo + "Scope Actual" y "Documentos Pendientes" priorizados |
| **ADR-010** (Phase 1 Scope Reduction) | Gap crítico ya identificado en auditoría previa — nunca formalizado |
| **ADR-011** (Claude as Primary AI Provider) | Nuevo requisito (suscripción Claude activa). Routing por Execution Level: Claude en L2-L4, gratuito solo en L1 |
| **ADR-003-A** (Telegram Interim Channel) | Divergencia WhatsApp/Telegram nunca formalizada. Formaliza Telegram como interino, WhatsApp como destino |
| **DOC-0031** (Security and Multi-Tenancy Handbook) | Requisito de "apto para multiusuario". Checklist de RLS, dos niveles de API keys, testing obligatorio, referencia directa al bug de multi-tenancy ya corregido |

### Decisión editorial explícita (pushback constructivo)

El plan maestro original lista 30 documentos adicionales (DOC-31 a DOC-60). **No se generaron los 30 en esta pasada** — producir 30 documentos senior sin contexto adicional generaría relleno de baja calidad. Se identificaron **2 como bloqueantes** (Data Model Specification, Testing Strategy); el resto es backlog iterativo. Ver sección 6 del índice maestro.

### ADR-003 (WhatsApp vs Telegram)

Resuelto — ver ADR-003-A. Ya no es un punto abierto.

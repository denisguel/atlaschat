# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : DOC-0031
# DOCUMENT    : Security and Multi-Tenancy Handbook
# VERSION     : 1.0
# STATUS      : Accepted
# CLASS       : Engineering Handbook
#
# REFERENCES
#
# DOC-0011 Database Architecture (declara el principio de RLS)
# ARCH-0028 AI Safety and Guardrails
# ADR-001 Supabase as Foundation
#
# ============================================================================

# 1. Propósito

DOC-0011 declara que el aislamiento de tenants es obligatorio y que RLS es la mecánica de enforcement. Este documento es el **manual operativo**: cómo implementarlo, cómo testearlo, y cómo evitar la clase de bug ya detectada en producción (fuga de datos multi-tenant en queries de competidores, encontrada y corregida en revisión de código de atlas-mvp).

Este documento existe porque **declarar el principio no fue suficiente** — el bug real ocurrió con el principio ya documentado en DOC-0011. La causa no fue desconocimiento, fue ausencia de checklist operativo y de test automatizado.

---

# 2. Modelo de aislamiento

- Cada tabla con datos operativos de una propiedad tiene columna `property_id` (o `tenant_id` según corresponda).
- **Ninguna tabla queda exenta** — incluyendo tablas de soporte (logs, cache, resultados de scraper) que en apariencia no parecen "datos de negocio" pero pueden filtrar información competitiva o de pricing entre tenants.
- RLS se activa a nivel de Postgres (`ENABLE ROW LEVEL SECURITY`), **no** a nivel de aplicación. La aplicación nunca debe ser la única barrera.

## Clase de bug ya identificada (competitor pricing queries)

El bug corregido: queries de scraping/análisis de competidores no filtraban correctamente por `property_id`, permitiendo que datos de precios de competidores de la Propiedad A fueran visibles/mezclados en el contexto de la Propiedad B.

**Patrón de causa raíz:** tablas de "soporte" (resultados de scraper, cache de análisis) se trataron como globales por default, no como tenant-scoped. Cualquier tabla nueva debe asumir `tenant_id` obligatorio salvo justificación explícita documentada.

---

# 3. Checklist de RLS por tabla nueva (obligatorio pre-merge)

- [ ] ¿La tabla tiene columna `property_id`/`tenant_id`? Si no, ¿está documentado por qué es intencionalmente global?
- [ ] ¿RLS está `ENABLED` en la tabla (no solo declarado, verificado con `\d+ tablename` o `pg_tables`)?
- [ ] ¿Existe policy de `SELECT` que filtre por el tenant del usuario autenticado?
- [ ] ¿Existe policy de `INSERT`/`UPDATE`/`DELETE` equivalente (no solo lectura)?
- [ ] ¿Se testeó con **dos tenants de prueba simultáneos** que un usuario del Tenant A no puede leer/escribir datos del Tenant B?
- [ ] Si la tabla es alimentada por un proceso de background (cron, scraper, n8n workflow) — ¿ese proceso también respeta el filtro, o corre con service-role bypasseando RLS? Si bypassea, **doble validación explícita en el código del worker**.

---

# 4. Arquitectura de dos niveles de API keys (ya establecida, formalizada aquí)

| Nivel | Ubicación | Alcance |
|---|---|---|
| **Sistema** | Vercel environment variables | Claves de proveedores de IA (Claude, modelo gratuito), infraestructura compartida |
| **Propietario** | Tabla `property_integrations` (Supabase, RLS activo) | Claves de integraciones específicas de cada propiedad (PMS, canal de mensajería, OTA) |

**Regla dura:** una clave de nivel Sistema nunca se expone a lógica que procese datos de un tenant específico sin pasar por la capa de autorización — evita que un bug de aplicación escale a una fuga de credenciales de infraestructura completa.

---

# 5. Guardrails de IA + multi-tenancy (cross-referencia ARCH-0028 §5)

- Todo contexto recuperado para un agente de IA (Property Brain, Growth Brain) se consulta **ya filtrado por RLS a nivel de base de datos**, nunca confiando en que el prompt del LLM "no mezcle" tenants.
- Embeddings/vectores: namespace obligatorio por `tenant_id` en la capa de almacenamiento vectorial — un vector de la Propiedad A nunca debe ser recuperable en una búsqueda de similaridad iniciada por la Propiedad B, incluso si el contenido es genéricamente similar (ej. dos propiedades con descripciones parecidas).

---

# 6. Testing obligatorio (mínimo viable, previo a cualquier release)

1. **Test de aislamiento de lectura:** usuario autenticado como Tenant A no puede `SELECT` filas de Tenant B en ninguna tabla, vía API ni vía query directa con su rol.
2. **Test de aislamiento de escritura:** usuario de Tenant A no puede `INSERT`/`UPDATE` filas asociadas a Tenant B.
3. **Test de proceso de background:** workers que corren con service-role (bypass de RLS por diseño) tienen test unitario que verifica que el filtro de `tenant_id` está codificado explícitamente en la query, no delegado a RLS.
4. **Test de IA/contexto:** una consulta al Property Brain de la Propiedad A no retorna ningún fragmento de contexto de la Propiedad B, aun cuando ambas tengan datos similares.

---

# 7. Auditoría periódica

- Revisión trimestral de `pg_policies` contra el listado de tablas activas — cualquier tabla nueva sin policy asociada es un hallazgo de severidad alta.
- Usar `Supabase:get_advisors` (MCP) como primera línea de detección automática de tablas sin RLS antes de cada release mayor.

---

# 8. Consecuencias

**Facilita:**
- Plataforma genuinamente apta para multiusuario sin depender de disciplina manual repetida en cada PR
- Base para certificación de seguridad futura (SOC2-ready) si el negocio escala a clientes enterprise

**Dificulta:**
- Cada tabla nueva tiene fricción obligatoria de checklist — deliberado, es más barato que un segundo incidente de fuga de datos

**A revisar:**
- Automatizar el checklist de §3 como test de CI (actualmente es manual) — recomendado como parte de `engineering:deploy-checklist` una vez exista pipeline de CI/CD

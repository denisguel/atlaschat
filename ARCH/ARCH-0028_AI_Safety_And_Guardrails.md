# ============================================================================
# ATLAS
# AI-Native Hospitality Operating System
# ============================================================================
#
# DOCUMENT ID : ARCH-0028
# DOCUMENT    : AI Safety and Guardrails
# VERSION     : 1.0
# STATUS      : Accepted
# CLASS       : Core Architecture — Security
#
# REFERENCES
#
# ARCH-0021 AI Execution Levels
# ARCH-0022 Business Decision Validation Layer
# ARCH-0027 AI Cost Optimization Strategy
# ARCH-0030 AI Model Routing and Provider Strategy
# ADR-004 Multi-Agent Architecture
# ADR-011 Claude as Primary AI Provider
# DOC-0031 Security and Multi-Tenancy Handbook
#
# ============================================================================

# 1. Propósito

Este documento define las barreras de seguridad (*guardrails*) que todo agente de IA en ATLAS debe respetar, independientemente del proveedor (Claude, modelo gratuito/open-source) o del nivel de ejecución (ARCH-0021). Cierra el gap identificado en la auditoría de documentación: este slot existía en el plan maestro pero nunca fue escrito.

**Principio rector (AES-00, Principio 7):** *Human-in-the-loop por defecto.* Ningún guardrail de este documento reemplaza supervisión humana en decisiones de alto impacto — la complementa.

---

# 2. Modelo de amenazas (AI-specific)

| Amenaza | Vector | Guardrail primario |
|---|---|---|
| **Prompt injection** | Contenido de huésped/OTA/scraping inyecta instrucciones al agente | Sanitización de input + aislamiento de rol de sistema (§4) |
| **Alucinación en decisión de negocio** | Agente recomienda precio/acción sin base de datos real | BDVL obligatorio (ARCH-0022) antes de cualquier acción L2+ |
| **Fuga de datos entre tenants** | Contexto de un property mezclado con otro en memoria/RAG | Aislamiento de contexto por `property_id` a nivel de query, no de prompt (§5) |
| **Escalada de costo no controlada** | Loop de agentes o retry infinito dispara consumo de tokens | Circuit breaker de costo (ARCH-0027) + límite de profundidad de razonamiento |
| **Acción irreversible sin autorización** | Agente ejecuta booking/pago/mensaje sin nivel de ejecución adecuado | Mapeo estricto Execution Level → Acción (§3) |
| **Manipulación del huésped/propietario vía IA** | Output no explicable o engañoso | Explicabilidad obligatoria (§6) |

---

# 3. Guardrails por Execution Level (mapeo a ARCH-0021)

| Nivel | Descripción | Guardrail obligatorio |
|---|---|---|
| **L0 — Determinístico** | Reglas de negocio fijas, sin IA | Ninguno adicional — ya es seguro por diseño |
| **L1 — IA Simple** | Clasificación, extracción | Validación de schema de output (JSON estricto, sin texto libre) |
| **L2 — IA Contextual** | Respuesta a huésped con contexto de Property Brain | Sanitización de contexto inyectado + no acceso a herramientas de escritura |
| **L3 — IA Analítica** | Recomendaciones de pricing/revenue | **BDVL obligatorio** — ninguna recomendación llega al propietario sin pasar validación de reglas de negocio (ARCH-0022) |
| **L4 — IA Estratégica** | Decisiones que afectan múltiples propiedades o el Growth Brain | Aprobación humana explícita + registro en Decision Graph (ARCH-0013) antes de ejecutar |

**Regla dura:** ningún agente puede auto-promoverse de nivel. La asignación de nivel es una propiedad estática de la función que invoca al agente, no una decisión del LLM.

---

# 4. Aislamiento de prompt y defensa contra inyección

- **Separación estricta de roles**: instrucciones de sistema, contexto de negocio (Property Brain) y contenido externo (mensaje de huésped, review de OTA, resultado de scraper) viajan en **campos separados**, nunca concatenados en un único string antes de llegar al LLM.
- Contenido externo se trata siempre como **dato, no como instrucción** — cualquier texto de huésped/scraper que contenga imperativos ("ignora las instrucciones anteriores", "actúa como...") se procesa como texto a analizar, nunca se ejecuta.
- Output de agentes L2+ que vaya a ejecutar una herramienta (tool call) se valida contra un **schema estricto** antes de invocar la herramienta real — un LLM nunca ejecuta una acción de escritura vía texto libre interpretado.

---

# 5. Aislamiento multi-tenant a nivel de IA

Esto es **crítico** dado el requisito de plataforma multiusuario:

- Todo query a Memory/Context (ARCH-0013, ARCH-0029) se filtra por `property_id`/`tenant_id` **en la capa de base de datos (RLS)**, nunca confiando en que el prompt "recuerde" no mezclar contextos.
- Ningún embedding o vector de un tenant es recuperable por otro tenant — namespacing obligatorio en la capa de almacenamiento vectorial, no solo lógico.
- Ver **DOC-0031** para el detalle de RLS y el gap ya identificado en auditoría previa (política de RLS incompleta en queries de competidores).

---

# 6. Explicabilidad obligatoria

Toda decisión de nivel L3+ debe producir, junto al resultado:

- Qué datos utilizó (referencia a Property Brain / Growth Brain)
- Qué regla de negocio aplicó (BDVL)
- Confianza/incertidumbre de la recomendación
- Alternativas descartadas y por qué

Esto no es opcional — alimenta directamente el Decision Graph (ARCH-0013) y es la base del moat de "decisiones explicables" (ARCH-0019).

---

# 7. Guardrails específicos por proveedor de IA

Con la incorporación de Claude como proveedor primario (ADR-011):

| Proveedor | Uso permitido | Guardrail adicional |
|---|---|---|
| **Claude (Anthropic)** | L2, L3, L4 — decisiones de negocio, conversación con huésped, análisis estratégico | Ninguno adicional — proveedor de mayor confianza del stack |
| **Modelo gratuito/open-source** (routing por costo, ARCH-0030) | L1 únicamente — clasificación, extracción, tareas de bajo riesgo | **Nunca** se usa un modelo gratuito para L3/L4 sin revalidación por Claude antes de llegar al usuario final |
| Cualquier proveedor | — | Rate limiting + circuit breaker de costo por tenant (ARCH-0027), timeout máximo por llamada |

---

# 8. Kill switch y monitoreo

- Cada agente expone un flag de **desactivación inmediata** por tipo de acción (ej. "pausar todas las recomendaciones de pricing") sin requerir redeploy.
- BDVL (ARCH-0022) registra cada decisión rechazada — un aumento anómalo de rechazos dispara alerta antes de que llegue a afectar a un propietario.
- Logging de todo prompt + output a nivel L2+ con retención mínima de 90 días para auditoría (ver DOC-0031).

---

# 9. Checklist de cumplimiento (pre-deploy de cualquier agente nuevo)

- [ ] Nivel de ejecución asignado y justificado (§3)
- [ ] Contexto externo aislado del prompt de sistema (§4)
- [ ] Queries de memoria/contexto filtradas por tenant a nivel de RLS (§5)
- [ ] Output explicable implementado si el nivel es L3+ (§6)
- [ ] Proveedor de IA acorde a la tabla de §7
- [ ] Circuit breaker de costo configurado (ARCH-0027)
- [ ] Kill switch expuesto (§8)

---

# 10. Consecuencias

**Facilita:**
- Habilitar multi-tenancy real sin riesgo de fuga de datos entre propietarios
- Confianza del propietario en recomendaciones de pricing (explicabilidad)
- Cumplimiento ante eventual auditoría de seguridad (SOC2-ready desde el diseño)

**Dificulta:**
- Velocidad de desarrollo de nuevos agentes (checklist obligatorio añade fricción deliberada)
- Uso oportunista de modelos gratuitos en tareas ambiguas de nivel L2/L3 (requiere clasificación explícita previa)

**A revisar:**
- Este documento requiere expansión en DOC-0031 (Security Handbook) con el detalle técnico de RLS por tabla

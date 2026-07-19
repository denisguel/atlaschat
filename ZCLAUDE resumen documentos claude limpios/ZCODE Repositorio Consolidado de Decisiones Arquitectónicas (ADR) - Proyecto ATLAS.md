### Repositorio Consolidado de Decisiones Arquitectónicas (ADR) \- Proyecto ATLAS

Este repositorio centraliza las decisiones críticas tomadas por la oficina de arquitectura para el Proyecto ATLAS. Como un sistema operativo "AI-native" para la hospitalidad, ATLAS prioriza la separación de la inteligencia y la ejecución, el aislamiento multi-tenant y la evolución guiada por eventos. Este documento sirve como la fuente única de verdad técnica (SSOT) para el equipo de desarrollo.

#### 1\. Índice Maestro de Decisiones

ID,Título,Estado,Fecha,Clasificación  
ADR-002,Adopción de Arquitectura Orientada a Eventos (EDA),Accepted,2026-06-29,Critical  
ADR-003,"Arquitectura ""WhatsApp First"" como Interfaz Primaria",Accepted,2026-06-29,Strategic  
ADR-003-A,Telegram como Canal Interino de Phase 1 (Enmienda),Accepted,2026-07-03,Strategic  
ADR-004,Arquitectura Multi-Agente vs. IA General,Accepted,2026-06-29,Critical  
ADR-005,"Arquitectura de Memoria Basada en ""Brains""",Accepted,2026-06-29,Critical  
ADR-007,Diseño de Arquitectura MCP-Ready,Accepted,2026-06-29,Strategic  
ADR-008,Abstracción de Proveedores de IA (Intelligence Layer),Accepted,2026-06-29,Critical  
ADR-009,Preparación para Event Sourcing,Accepted,2026-06-29,Strategic  
ADR-010,Reducción de Alcance a Phase 1 — Revenue Agent Únicamente,Accepted,2026-07-03,Critical  
ADR-010-D,Piso de Logging de Performance de Contenido,Accepted,2026-07-03,Operational  
ADR-011,Claude como Proveedor Primario y Routing por Costo,Accepted,2026-07-03,Critical  
ADR-011-A,Override de Proveedor Gratuito para Testing (Enmienda),Accepted,2026-07-05,Operational  
ADR-012,Discoverability Agéntica (MCP) como Prioridad de Fase 2,Accepted,2026-07-03,Strategic  
ADR-013,Reescritura de la App sobre Data Model DOC-0032,Accepted,2026-07-04,Critical  
ADR-014,Techo de Precio Externo (Propuesta),Proposed,2026-07-08,Strategic  
ADR-015,Competitor Scout Automático y Moat de Datos,Proposed,2026-07-08,Strategic

#### 2\. Arquitectura de Comunicación y Eventos (Serie Core)

##### ADR-002: Adopción de Arquitectura Orientada a Eventos (EDA)

Se adopta EDA como el modelo primario para garantizar el acoplamiento débil entre agentes de IA y servicios de dominio.  *Referencia técnica: ARCH-0015, ARCH-006.*

1. **Decision Drivers:**  Escalabilidad horizontal, independencia de agentes autónomos, capacidad de  *replay*  para entrenamiento de IA y auditoría.  
2. **Categorías de Eventos Obligatorias:**  
3. **Domain:**  Cambios de estado de negocio (e.g., ReservationConfirmed).  
4. **AI:**  Razonamiento y planes (e.g., IntentDetected, PlanGenerated).  
5. **Integration:**  Webhooks y mensajería (e.g., WhatsAppMessageReceived).  
6. **Infrastructure:**  Operaciones de sistema (e.g., RetryScheduled).  
7. **Metadatos del Event Store:**  event\_id, aggregate\_id, aggregate\_type, version, tenant\_id, timestamp, payload, correlation\_id y causation\_id.  
8. **Restricción Técnica:**  El orden de los eventos se garantiza únicamente dentro de un mismo agregado (e.g., una reserva específica).

##### ADR-009: Preparación para Event Sourcing

ATLAS utiliza PostgreSQL para el estado operativo, pero trata los eventos como la verdad histórica definitiva.  *Referencia técnica: ARCH-0024.*\!IMPORTANT  **Regla de Consistencia:**  "Commit antes de Publicar". Los eventos deben persistirse en la tabla de  *Outbox*  dentro de la misma transacción de base de datos que el cambio de estado para evitar inconsistencias.

#### 3\. Interfaz de Usuario y Canales (Serie Interfaz)

##### ADR-003: Arquitectura "WhatsApp First"

Principio rector:  **"La conversación es la interfaz"** . El sistema debe adaptarse al comportamiento del usuario en LATAM.  *Referencia técnica: ARCH-003.*| Componente | Responsabilidades || \------ | \------ || **Interfaz WhatsApp** | Operación diaria, gestión de reservas, limpieza, comunicación con huéspedes y generación de contenido. || **Dashboard Web** | Onboarding, configuración de propiedades, visualización de analíticas avanzadas y facturación. |

##### ADR-003-A: Enmienda — Telegram como Canal Interino de Phase 1

Debido a la fricción de la API de Meta, se formaliza Telegram para iterar el MVP.

* **Justificación:**  Acelerar Phase 1 sin depender de aprobaciones de terceros.  
* **Aislamiento:**  La lógica de negocio debe usar un  *Conversation Adapter*  agnóstico al canal para permitir el cambio a WhatsApp sin reescritura.

#### 4\. Inteligencia Artificial y Modelos de Agentes (Serie AI)

##### ADR-004: Arquitectura Multi-Agente vs. IA General

ATLAS descompone la inteligencia en agentes especializados para reducir alucinaciones y optimizar ventanas de contexto.  *Referencia técnica: ARCH-0014, RFC-0002.*

* **Orchestrator Agent:**  Coordina el flujo, selecciona agentes de dominio y construye planes, pero  **nunca**  ejecuta lógica de negocio.  
* **Agentes de Dominio:**  
* **Guest Relations:**  Comunicación y tono de hospitalidad.  
* **Revenue:**  Optimización de precios y análisis de demanda.  
* **Operations:**  Coordinación de limpieza y mantenimiento.  
* **Conversion:**  Optimización de ventas y calificación de leads.  
* **Knowledge:**  Recuperación semántica y búsqueda en base de conocimiento.

##### ADR-008: Abstracción de Proveedores de IA

Creación de una "Intelligence Layer" para evitar el  *vendor lock-in* . Los agentes solicitan capacidades (e.g., "razonamiento estratégico"), no modelos específicos.  *Referencia técnica: ARCH-0030.*

##### ADR-011: Claude como Proveedor Primario y Routing por Costo

Se establece la jerarquía de modelos basada en el impacto de la decisión.  *Referencia técnica: ARCH-0021.*| Nivel de Ejecución | Proveedor Asignado | Ejemplo de Tarea || \------ | \------ | \------ || **L0 — Determinístico** | Ninguno (Código/Reglas) | Validaciones de esquema, cálculos fijos. || **L1 — IA Simple** | Modelo Gratuito (Groq/Gemini) | Clasificación de mensajes, tagging. || **L2 — IA Contextual** | Claude | Conversación con el huésped. || **L3 — IA Analítica** | Claude | Recomendaciones de Pricing (Revenue Agent). || **L4 — IA Estratégica** | Claude | Growth Brain y decisiones multi-propiedad. |  
\!CAUTION  **REGLA DURA:**  Un modelo gratuito NUNCA debe ser el último eslabón antes de una decisión L3/L4 visible al usuario. Claude es el validador obligatorio para decisiones de alto impacto.

##### ADR-011-A: Enmienda — Override de Proveedor Gratuito para Testing

Permiso temporal para usar Groq/Gemini en niveles L3 exclusivamente durante pruebas de arquitectura si el saldo de la API primaria es insuficiente. Reversión obligatoria antes del uso productivo.

#### 5\. Memoria y Contexto (Serie Brain)

##### ADR-005: Arquitectura de Memoria Basada en "Brains"

La memoria se define como una  **capa de recuperación semántica, no un estado de negocio** .  *Referencia técnica: ARCH-0013.*

* **Guest Brain:**  Preferencias históricas y perfiles de huéspedes.  
* **Property Brain:**  Conocimiento operativo y reglas de la casa.  
* **Growth Brain:**  Inteligencia comercial y performance de campañas.  
* **Política de Actualización:**  Los Brains son asíncronos y se actualizan exclusivamente mediante el consumo de eventos, garantizando que el agente sea siempre  *stateless* .

#### 6\. Evolución, Estándares y Ecosistema (Serie Estratégica)

##### ADR-007: Arquitectura MCP-Ready

Diseño alineado con el  *Model Context Protocol* . Las capacidades del sistema se exponen como "Tools" independientes, permitiendo la interoperabilidad futura con agentes externos.

##### ADR-012: Discoverability Agéntica (MCP) como Prioridad de Fase 2

Cambio estratégico para priorizar la exposición de propiedades como servidores MCP, permitiendo que IAs externas (e.g., ChatGPT) descubran y reserven propiedades directamente.

##### ADR-014: Techo de Precio Externo (Propuesta)

Incorporación de un  *guardrail*  de demanda basado en el contexto macroeconómico (e.g., Argentina vs. Brasil). Permite al propietario setear un techo que el Revenue Agent no puede exceder.

##### ADR-015: Competitor Scout Automático (Propuesta)

Automatización del descubrimiento de competidores.

* **Moat Técnico:**  Implementación del  **Decision Graph**  (aprendizaje sobre las decisiones de aceptación/rechazo del propietario).  
* **Seguridad Crítica:**  Los trabajadores de scraping deben filtrar explícitamente por property\_id incluso con RLS activo para evitar fugas de datos entre inquilinos detectadas en auditorías previas.

#### 7\. Implementación y Alcance del MVP (Serie Operativa)

##### ADR-010: Reducción de Alcance a Phase 1 — Revenue Agent Únicamente

Se pausa el desarrollo de la plataforma completa (NeuralCore) para validar el Revenue Agent.

* **Fuera de alcance:**  AI Orchestrator completo, Multi-Agent System completo, Brains compartidos.  
* **Prerrequisito duro:**  Registro estructurado de decisiones (Logging BDVL).  *Referencia: ARCH-0018.*

##### ADR-010-D: Piso de Logging de Performance de Contenido

Se exige el uso de  **UTM tracking**  en cada link publicado y la instrumentación de tablas content\_assets y content\_performance para evitar la pérdida de datos históricos de conversión desde el día uno.

##### ADR-013: Reescritura de la App sobre Data Model DOC-0032

Descarte del código pre-suscripción de Claude (commits anteriores al  **901eb02** ) para alinearse con el aislamiento multi-tenant y el modelo de datos canónico.

* **Seguridad:**  Implementación obligatoria de Row Level Security (RLS) y el  *Custom Access Token Hook*  para garantizar la integridad de los datos de los inquilinos.  *Referencia: DOC-0031.*

#### 8\. Matriz de Trazabilidad de Decisiones

##### Relaciones e Interdependencias por Impacto

* **Seguridad y Multi-tenancy:**  
* ADR-013 (Reescritura por RLS)  
* ADR-015 (Filtro explícito property\_id en scrapers/workers)  
* DOC-0031 (Manual de Seguridad)  
* **Moat de Datos y Aprendizaje Agéntico:**  
* ADR-005 (Sistemas Brain como retrieval)  
* ADR-010-D (UTM y logging de performance)  
* ADR-015 (Decision Graph y feedback del propietario)  
* **Agilidad de IA y Control de Costos:**  
* ADR-008 (Capa de Abstracción)  
* ADR-011 (Política de Routing L0-L4)  
* ARCH-0027 (Optimización de costos)**Nota final:**  Este documento es normativo. Para detalles de implementación técnica, esquemas de base de datos o contratos de herramientas, consulte los documentos  **ARCH** ,  **RFC**  y  **DOC**  referenciados en cada sección.


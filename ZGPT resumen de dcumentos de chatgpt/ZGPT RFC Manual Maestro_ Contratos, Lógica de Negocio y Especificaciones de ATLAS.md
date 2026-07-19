### Manual Maestro: Contratos, Lógica de Negocio y Especificaciones de ATLAS

Este documento constituye la especificación técnica canónica y el marco normativo para el desarrollo de ATLAS. Define los contratos arquitectónicos, la lógica de negocio y las fronteras de ejecución entre los sistemas determinísticos y los componentes de inteligencia artificial. Como Directriz Principal, este manual actúa como la "Única Fuente de Verdad" (SSOT) para la ingeniería del sistema.

#### 1\. Fundamentos Arquitectónicos y Filosofía AI-Native

ATLAS no es un software con funciones de IA; es un sistema operativo de hospitalidad construido desde el núcleo para ser operado por agentes.

##### Principios de Producto (01\_PRODUCT\_PRINCIPLES.md)

1. **AI Native** : La IA es el núcleo. Si un agente puede resolver un problema, la arquitectura DEBE diseñarse alrededor del agente, no de formularios web.  
2. **Memory First** : Toda interacción (conversaciones, decisiones, cancelaciones) DEBE alimentar la memoria del sistema. La información no se descarta.  
3. **Context Before Prompt** : Ninguna respuesta de LLM debe generarse sin contexto previo extraído de los "Brains".  
4. **Event Driven** : El sistema reacciona a cambios de estado inmutables (ReservationCreated, PriceUpdated) en lugar de consultas activas.  
5. **Automation First** : Las tareas repetitivas DEBEN transformarse en flujos de trabajo (workflows), minimizando clics manuales.  
6. **Human in the Loop** : Todo flujo define su autonomía: Nivel 1 (IA recomienda), Nivel 2 (IA propone/Humano aprueba), Nivel 3 (IA ejecuta).  
7. **WhatsApp First** : WhatsApp es la interfaz operativa primaria. La web es complementaria y administrativa.  
8. **API First** : La interfaz no contiene lógica crítica; toda funcionalidad debe estar disponible vía APIs internas.  
9. **Stateless Agents** : Los agentes no retienen memoria propia; dependen de PostgreSQL y Vector Stores para persistencia.  
10. **Single Source of Truth (SSOT)** : Cada dato tiene un único origen oficial (ej. Disponibilidad en Calendario Interno).  
11. **Modularidad** : Módulos (Revenue, Messaging, Cleaning) operan con bajo acoplamiento y responsabilidades claras.  
12. **Escalabilidad Horizontal** : Diseñado para escalar de una propiedad a miles sin rediseño arquitectónico.  
13. **Cost Efficiency** : Prioridad en servicios serverless y optimización de tokens para proteger el margen operativo.  
14. **Security by Design** : Autenticación, auditoría y cifrado son requisitos de diseño obligatorios.  
15. **Explainability** : Toda decisión automática DEBE ser explicable para el propietario (ej. "¿Por qué cambió el precio?").  
16. **Observability** : Todo proceso debe generar logs, métricas y trazas auditables.  
17. **Progressive Intelligence** : Evolución por fases: Asiste \-\> Sugiere \-\> Automatiza \-\> Optimiza \-\> Predice.  
18. **MVP First** : Desarrollo exclusivo de capacidades que aumenten ingresos o reduzcan trabajo operativo.  
19. **Hospitality Native** : Decisiones basadas estrictamente en las necesidades del sector de alojamiento.  
20. **Evolución Compatible** : Los cambios que rompan compatibilidad DEBEN documentarse mediante una Decisión Arquitectónica (ADR).

##### Checklist para Nuevas Funcionalidades

*  ¿Respeta el principio AI Native?  
*  ¿Genera eventos inmutables?  
*  ¿Alimenta la memoria del sistema?  
*  ¿Es automatizable sin intervención humana?  
*  ¿Escala horizontalmente?  
*  ¿Expone funcionalidad vía API?  
*  ¿Cumple con estándares de seguridad y auditoría?  
*  ¿Reduce el trabajo manual del propietario?  
*  ¿Aporta valor medible al MVP?

#### 2\. Modelo de Capas del Sistema (System Layers)

ATLAS implementa un modelo de capas estrictas (ARCH-002) donde la comunicación es secuencial y las responsabilidades están aisladas.**Flujo Lógico de Información:**  Presentación \-\> Conversación \-\> Orquestación AI \-\> Servicios de Dominio \-\> Infraestructura \-\> Persistencia| Capa | Responsabilidad Principal | Restricción Crítica || \------ | \------ | \------ || **Presentación/Conversación** | Captura de intención y normalización de entrada (WhatsApp/Web). | Prohibido contener lógica de negocio o validaciones de dominio. || **Orquestación AI** | Interpretación de intención, selección de agentes y construcción de contexto. | Prohibido el acceso directo a la base de datos o almacenamiento. || **Servicios de Dominio** | Ejecución determinística de comandos (Reservas, Pricing, PMS). | Prohibido incluir lógica de razonamiento o llamadas a LLMs. || **Infraestructura/Eventos** | Comunicación asíncrona, orquestación de side-effects y APIs externas. | Prohibido modificar el estado del negocio de forma directa. || **Persistencia (Base de Soporte)** | Almacenamiento del estado operativo (SSOT) y logs de eventos. | El acceso debe ser mediado exclusivamente por Servicios de Dominio. |

#### 3\. Contrato de Orquestación y Niveles de Ejecución AI (ARCH-0021)

Toda petición de entrada DEBE pasar por la  **Pirámide de Ejecución AI**  para garantizar eficiencia en costos y latencia.

##### Flujo de Entrada Técnico

1. **Normalization** : Limpieza y formateo de la entrada.  
2. **Intent Detection** : Clasificación de la intención del usuario.  
3. **Complexity Estimation** : Determinación del nivel de razonamiento necesario (L0-L4).

##### AI Execution Levels

Nivel,Complejidad,Latencia,Costo,Descripción / Ejemplo  
L0,Nula (Reglas),\< 500ms,\~0,"Respuestas estáticas (FAQ, ""Gracias"")."  
L1,Baja (LLM Lite),\< 2s,Bajo,"Traducción, ajustes de tono o saludos."  
L2,Media (Agente),2s \- 5s,Medio,"Consulta al Brain (ej. ""¿Cuándo pagué la última vez?"")."  
L3,Alta (Multi-Agente),5s \- 15s,Alto,Razonamiento cross-domain (Mover reserva \+ ajuste precio).  
L4,Estratégica,Asíncrona,Muy Alto,Optimización de portafolio y planificación estratégica.

#### 4\. Capa de Validación de Decisiones de Negocio (BDVL \- ARCH-0022)

La BDVL actúa como el "Supervisor" mandatorio entre el razonamiento probabilístico de la IA y la ejecución determinística.**Principio de Inmutabilidad** : Ninguna IA ejecuta acciones de negocio directamente. Todo resultado de la IA es una propuesta que DEBE ser validada por la BDVL basándose en políticas determinísticas.

##### Clasificación de Riesgo y Validación

* **Riesgo Crítico** : Cancelaciones, reembolsos, eliminación de entidades.  
* *Requerimiento* : Aprobación humana obligatoria SIEMPRE.  
* **Riesgo Alto** : Modificación de reservas, ajustes de precios, cambios de disponibilidad.  
* *Requerimiento* : Validación de política \+ Confirmación explícita del usuario.  
* **Riesgo Medio** : Creación de tareas, notas de huéspedes, envío de instrucciones.  
* *Requerimiento* : Validación automática de políticas de negocio.  
* **Riesgo Bajo** : Respuestas FAQ, generación de contenido, resúmenes.  
* *Requerimiento* : Ejecución automática tras validación de confianza (Confidence Score).

#### 5\. El Sistema Multi-Agente Lógico (ARCH-0014 / RFC-0004)

ATLAS implementa agentes como componentes lógicos especializados. No son microservicios físicos, sino fronteras de razonamiento en el backend.

##### Guest Agent

* **Misión** : Maximizar la satisfacción y retención del huésped.  
* **Brain** : Guest Brain.  
* **Tools** : Messaging Tool, Recommendation Tool, Guest Tool.

##### Reservation Agent

* **Misión** : Gestionar el ciclo de vida de reservas y validar integridad del calendario.  
* **Brain** : Property Brain.  
* **Tools** : Reservation Tool, Availability Tool, Booking Tool.

##### Revenue Agent

* **Misión** : Maximizar la rentabilidad (RevPAR) mediante análisis de mercado.  
* **Brain** : Revenue Brain.  
* **Tools** : Pricing Tool, Revenue Tool, Competitor Tool.

##### Growth Agent

* **Misión** : Incrementar reservas directas y optimizar conversión de leads.  
* **Brain** : Growth Brain.  
* **Tools** : Campaign Tool, Content Tool, Attribution Tool.

##### Operations Agent

* **Misión** : Coordinar tareas operativas y mantenimiento de unidades.  
* **Brain** : Property Brain.  
* **Tools** : Task Tool, Maintenance Tool, Cleaning Tool.

##### Property Agent

* **Misión** : Gestión integral del conocimiento operativo y reglas de propiedad.  
* **Brain** : Property Brain.  
* **Tools** : Metadata Tool, Policy Tool, Amenity Tool.

##### Knowledge Agent

* **Misión** : Recuperación semántica pura, resumen de fuentes y atribución de conocimiento.  
* **Brain** : Todos (vía Context Builder).  
* **Tools** : Search Tool, Summary Tool, Retrieval Tool.

#### 6\. Estrategia de Memoria y Contexto (Brains \- ARCH-0029 / ARCH-0027)

La inteligencia es proporcional a la calidad del contexto recuperado.

* **Guest Brain** : Preferencias, historial de comportamiento y valor de vida del huésped.  
* **Property Brain** : Reglas de la casa, manuales, inventario y guías operativas.  
* **Revenue Brain** : Patrones de demanda, histórico de precios y elasticidad.  
* **Growth Brain** : Atribución, éxito de campañas y embudos de conversión.

##### Pipeline de Contexto y Restricciones de Diseño

El  **Context Builder**  debe ejecutar el flujo: Retrieval \-\> Ranking \-\> Compression \-\> Assembly.**Contrato de Context Budget** : El presupuesto de tokens es una restricción de diseño obligatoria para el control de costos:

* **L1** : \< 2k tokens.  
* **L2** : \< 6k tokens (Priorizar ranking de relevancia semántica).  
* **L3** : \< 12k tokens (Requiere compresión agresiva de historial).  
* **L4** : Dinámico (Asíncrono).

#### 7\. Modelo de Consistencia de Estado y Persistencia (ARCH-0024)

PostgreSQL es la Única Fuente de Verdad (SSOT). Los eventos describen hechos pasados, pero el estado reside en la base de datos relacional.

##### Jerarquía de Datos (SSOT)

Organization (Tenant)  
└── Property  
    └── Unit  
        └── Reservation  
            └── Guest

##### Contrato de Consistencia: "Commit Before Publish"

Para garantizar que la IA no reaccione a datos inexistentes:

1. Se ejecuta la transacción en PostgreSQL.  
2. Se registra el evento en la tabla Transactional Outbox dentro de la misma transacción.  
3. Tras el  **Commit** , el publicador despacha el evento al Bus.

#### 8\. Arquitectura Orientada a Eventos (ADR-002)

Categorías de Eventos inmutables y sus propietarios:

* **Domain** : ReservationCreated (Reservation Service), PriceUpdated (Pricing Service).  
* **AI** : IntentDetected, PlanGenerated.  
* **Integration** : WhatsAppMessageReceived.  
* **Infrastructure** : RetryScheduled.

#### 9\. Capa de Ejecución de Herramientas (Tool Invocation \- RFC-0010)

Las Tools son el contrato mediante el cual la IA solicita cambios determinísticos.**Contrato Obligatorio de Tools** :

* **ID y Versión** : Identificador único inmutable.  
* **JSON Schema** : Esquemas estrictos de entrada/salida.  
* **Permisos** : RLS y roles requeridos (tenant\_id obligatorio).**Flujo de Despacho** : AI Proposal \-\> BDVL Validation \-\> Tool Dispatcher \-\> Domain Service

#### 10\. Estrategia de UX Operacional (ARCH-0023 / ADR-003)

ATLAS implementa una estrategia híbrida donde la interfaz se adapta a la densidad de información.| Característica | WhatsApp (Conversación) | Dashboard (Operacional) || \------ | \------ | \------ || **Uso Ideal** | Ejecución rápida, alertas, soporte. | Decisiones visuales, comparativas. || **Densidad** | Baja (Secuencial). | Alta (Paralela/Calendarios). || **Responsabilidad** | Operación diaria "en movimiento". | Configuración y análisis denso. |  
**Operaciones Exclusivas WhatsApp** : Consultas de disponibilidad rápida, registro de limpieza, alertas de precio, interacción directa con huéspedes y generación de contenido RRSS.

#### 11\. Especificaciones de Servicios de Dominio (PMS & Revenue)

##### Lógica PMS (RFC-0006)

Define entidades inmutables. Una reserva DEBE estar vinculada a una Unit. Los estados de disponibilidad (Available, Reserved, Blocked, Maintenance) rigen toda la lógica de colisiones en el calendario.

##### Revenue Intelligence (RFC-0008)

El sistema genera recomendaciones basadas en señales:

* **Internas** : Booking Pace (ritmo de reserva), velocidad de llenado y cancelación.  
* **Externas** : Clima, eventos locales detectados, precios de competencia.  
* **Revenue Opportunity Score** : Cada oportunidad de ingreso se clasifica por impacto y confianza antes de proponerse al dueño. Las recomendaciones DEBEN ser explicables y basadas en elasticidad de precio comprobada.

#### 12\. Infraestructura, Escalabilidad y Seguridad (ARCH-0025)

**MVP Physical Architecture** : Modular Monolith.

* **Stack** : Next.js, Supabase (Postgres, Auth, Storage), n8n (Side-effects orchestration).  
* **Seguridad** : Row Level Security (RLS) en Postgres es la barrera final de multi-tenancy.  
* **Cost Optimization** : Model Routing (uso de GPT-4o-mini para L1/L2 y modelos pesados solo para L3/L4). Implementación obligatoria de  **Semantic Cache**  para preguntas redundantes.

#### 13\. Roadmap de Implementación y Evolución (RFC-0018 / ARCH-0019)

##### MVP Roadmap (Phases 0 \- 10\)

* **Phase 0: Foundation** : Infraestructura CI/CD y Supabase.  
* **Phase 1: Core Platform** : Entidades PMS y gestión de organizaciones.  
* **Phase 2: AI Foundation** : Orquestador, Context Builder y BDVL.  
* **Phase 3: Guest Ops** : Integración WhatsApp y Guest Agent.  
* **Phase 4: Revenue Intelligence** : Brain de Revenue y sistema de recomendaciones.  
* **Phase 5: Growth Intelligence** : Atribución y conversión de leads.  
* **Phase 6: Workflow Automation** : Workflows operativos en n8n.  
* **Phase 7: Booking Engine** : Motor de reservas directas SEO-friendly.  
* **Phase 8: Operational Intelligence** : Operations Agent y mantenimiento.  
* **Phase 9: Analytics** : Dashboards de KPIs reales.  
* **Phase 10: Production Hardening** : Seguridad, load testing y optimización final de costos.

##### Estadios de Evolución

1. **Stage 1: AI-Assisted** : La IA asiste al humano en tareas manuales (MVP).  
2. **Stage 2: Intelligent Ops** : IA predice necesidades operativas.  
3. **Stage 3: Autonomous Optimization** : Ejecución bajo políticas pre-aprobadas.  
4. **Stage 4: Hospitality OS** : La conversación es la interfaz total de gestión.  
5. **Stage 5: Intelligence Platform** : Planificación estratégica autónoma y cross-market.

#### 14\. Glosario de Términos Críticos

* **SSOT (Single Source of Truth)** : PostgreSQL como origen único de verdad operativa.  
* **BDVL (Business Decision Validation Layer)** : Filtro determinístico entre IA y ejecución.  
* **MCP-Ready** : Arquitectura preparada para el Model Context Protocol.  
* **Brain** : Subsistema de memoria contextual especializada.  
* **Aggregate** : Conjunto de entidades tratadas como unidad (ej. Reservation).  
* **Context Builder** : Motor de ensamblaje de prompts optimizado por costo y relevancia.  
* **Transactional Outbox** : Patrón para sincronizar persistencia y eventos.  
* **Booking Pace** : Velocidad a la que se reciben reservas comparado con el histórico.


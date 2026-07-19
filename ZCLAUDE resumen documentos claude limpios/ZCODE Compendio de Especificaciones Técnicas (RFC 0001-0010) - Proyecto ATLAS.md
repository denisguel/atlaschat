### Compendio de Especificaciones Técnicas (RFC 0001-0010) \- Proyecto ATLAS

Este compendio consolida las especificaciones maestras del ecosistema ATLAS, definiendo la arquitectura, los protocolos de inteligencia y los estándares de ejecución del primer sistema operativo de hospitalidad nativo para inteligencia artificial (AI-Native) diseñado para el mercado latinoamericano.

#### 1\. Introducción al Ecosistema ATLAS

ATLAS se define como un  **AI-Native Hospitality Operating System** . Bajo este paradigma, la inteligencia artificial no es un componente periférico o un "chatbot" añadido, sino el núcleo (NeuralCore) que orquesta la lógica de negocio. La visión fundamental es que  **la IA es quien decide cómo utilizar el software, y no al revés** .El sistema se rige por cinco principios arquitectónicos críticos:

1. **AI-First:**  Toda funcionalidad se diseña bajo la premisa de que un agente debe ser capaz de resolverla; la interfaz de usuario es complementaria.  
2. **Memory-First:**  Toda interacción (conversaciones, decisiones, cancelaciones) genera conocimiento persistente que alimenta los Brains del sistema.  
3. **Context-First:**  Ningún LLM responde basándose únicamente en un prompt; se garantiza la inyección previa de contexto enriquecido (Guest, Property y Growth).  
4. **Event-Driven:**  El sistema es reactivo y asíncrono; los "Domain Events" disparan workflows y acciones agénticas proactivas.  
5. **WhatsApp First:**  WhatsApp es la interfaz primaria de operación y conversión; el dashboard web es una herramienta de soporte analítico y configuración.

#### 2\. RFC-0001: Arquitectura AI-Native

ATLAS sustituye el modelo tradicional "UI → Software → DB" por un flujo de inteligencia dirigida. La arquitectura asume que la inteligencia es el sistema y el software es la capa de ejecución determinística.**Regla Core:**  Toda decisión de negocio significativa es mediada por agentes. No existe ejecución crítica que no pase por una fase de interpretación agéntica.

* **Capa de Interpretación (Probabilística):**  Utiliza modelos de razonamiento (L4) para procesar lenguaje natural, extraer intenciones y formular planes de acción basados en contexto.  
* **Capa de Ejecución (Determinística):**  Recibe instrucciones estructuradas, las valida contra la capa de políticas (BDVL) y ejecuta cambios de estado en la base de datos o servicios externos.

#### 3\. RFC-0002: Orquestador de IA

El Orquestador es el componente central de coordinación. Su función es transformar solicitudes ambiguas en pipelines de ejecución seguros y observables, sin poseer lógica de negocio propia.

##### Ciclo de Vida de Ejecución

Cada interacción sigue estrictamente este ciclo de 9 pasos:

1. **Recepción de solicitud:**  Entrada vía API o Webhook (ej. mensaje de WhatsApp).  
2. **Normalización:**  Estandarización de formatos y metadatos.  
3. **Resolución de Nivel de Ejecución:**  Clasificación de la tarea desde L0 (determinística pura) hasta L4 (razonamiento profundo).  
4. **Construcción de Contexto:**  Recuperación de memoria relevante mediante el Context Builder.  
5. **Ejecución de Razonamiento:**  Invocación del agente especializado asignado.  
6. **Validación de Acciones (BDVL):**  Verificación de la propuesta contra políticas de riesgo y negocio.  
7. **Ejecución de Servicios:**  Llamada al Tool Dispatcher para acciones en el dominio.  
8. **Generación de Respuesta:**  Traducción de resultados a lenguaje natural o formato de salida.  
9. **Emisión de Eventos:**  Publicación de hechos inmutables en el bus de eventos.

##### Restricciones de Diseño

Responsabilidades,No-Responsabilidades  
Clasificar complejidad (L0-L4).,Persistir datos operacionales directamente.  
Coordinar el Context Builder.,Ejecutar reglas de negocio o cálculos de precio.  
Invocar agentes especializados.,Acceder a la base de datos sin servicios de dominio.  
Someter acciones a validación (BDVL).,Autorizar transacciones financieras por cuenta propia.

#### 4\. RFC-0003: Sistema de Memoria Persistente (Brains)

La arquitectura de "Brains" provee la inteligencia contextual necesaria para eliminar la amnesia de los modelos de lenguaje genéricos.

##### Catálogo de Brains

* **Guest Brain:**  Almacena perfiles persistentes, historial de sentimientos, preferencias de comunicación y valor de vida del cliente (LTV).  
* **Property Brain:**  Centraliza el conocimiento operativo (amenities, reglas de la casa, manuales) y preferencias técnicas del propietario sobre el activo.  
* **Growth Brain:**  Concentra la inteligencia comercial. Incluye el  **Content Performance Loop**  (qué hooks generan reservas) y la atribución de conversiones, especialmente el rastreo de interacciones en WhatsApp que derivan en cierres.

##### Estrategia de Recuperación

El Context Builder selecciona memoria relevante mediante una búsqueda híbrida:

1. **Similitud Semántica:**  Uso de embeddings y pgvector para encontrar conceptos relacionados.  
2. **Prioridad de Negocio:**  Filtrado por relevancia temporal (ej. una reserva activa tiene prioridad sobre una queja de hace dos años).

#### 5\. RFC-0004: Sistema de Inteligencia Multi-Agente

ATLAS utiliza agentes especializados y  **Stateless**  (Principio 9\) para maximizar la precisión y reducir costos de tokens.| Agente | Misión Principal | Tools Permitidas || \------ | \------ | \------ || **Orchestrator Agent** | Coordinador superior y ruteador de intenciones. | ContextBuilder, AgentDispatcher. || **Guest Relations** | Maximizar satisfacción y resolver dudas FAQ. | Messaging, GuestProfile, FAQ\_Search. || **Revenue** | Optimizar ingresos y rentabilidad. | Pricing, Availability, CompetitorScout. || **Operations** | Gestionar mantenimiento y limpieza. | MaintenanceTask, CleaningSchedule. || **Content** | Generar visibilidad y activos de marketing. | MediaAsset, SocialPoster, SEO\_Optimizer. || **Conversion** | Cerrar reservas y manejar objeciones. | BookingEngine, PaymentLink, CRM\_Update. |  
**Reglas de Comunicación:**  Se prohíbe la comunicación directa agente-a-agente. Toda mediación es responsabilidad obligatoria del Orquestador para garantizar auditabilidad y seguridad.

#### 6\. RFC-0005: Arquitectura Orientada a Eventos (Workflows)

El desacoplamiento del sistema se basa en "Domain Events", hechos inmutables que notifican cambios de estado.

* **Patrón de Outbox Transaccional:**  Para garantizar consistencia, los eventos se registran en una tabla outbox dentro de la misma transacción de PostgreSQL que el cambio de estado. Un proceso secundario publica el evento solo tras un commit exitoso.  
* **Tipos de Eventos:**  ReservationCreated, PriceUpdated, GuestCheckedOut. Estos disparan flujos asíncronos en n8n o Edge Functions (ej. notificar al personal de limpieza).

#### 7\. RFC-0006: Modelo de Dominio de Hospitalidad

Define el lenguaje ubicuo y la estructura de datos core (Organization, Property, Unit, Guest, Reservation).**Seguridad y Multi-Tenancy:**  El aislamiento de datos es absoluto. Se implementa mediante  **Row Level Security (RLS)**  a nivel de base de datos PostgreSQL. El organization\_id es el discriminador obligatorio en todas las políticas, mitigando riesgos de fuga de datos competitivos entre propiedades (referencia: bug detectado en Scraper DOC-0031).

#### 8\. RFC-0007: Motor de Reservas Directas

Plataforma de conversión inteligente que no se limita a capturar datos, sino que personaliza la oferta según el Guest Brain. Cada reserva alimenta el Growth Brain mediante metadatos de atribución, permitiendo al sistema identificar qué canales (Instagram, WhatsApp) tienen mayor retorno real.

#### 9\. RFC-0008: Inteligencia de Revenue (Revenue Intelligence)

ATLAS utiliza el  **USD como moneda canónica de cómputo**  para proteger la rentabilidad de la inflación, utilizando el ARS solo para display.

##### Lógica de Decisión

* **Modelo Noche:**  Precio diario con discriminación entre días de semana (L-J) y fines de semana (V-D).  
* **Modelo Paquete:**  Venta por estadía completa indivisible (ej. min\_nights \= 7 en alta temporada).

##### Factores de Optimización

La IA analiza: Ocupación, Booking Pace (ritmo de reserva vs. histórico), Competencia comparable y el  **Techo de Precio por Destino Alternativo**  (ej. si Mar de las Pampas supera el costo de un viaje a Brasil, la demanda se desplaza).

##### Explicabilidad Obligatoria

Toda recomendación de precio debe incluir 7 elementos:

1. Temporada y modelo aplicado. 2\. Competencia de referencia (con link verificado). 3\. Ocupación actual. 4\. Booking pace. 5\. Propuesta de cambio. 6\. Justificación estratégica. 7\. Proyección de impacto en revenue.

#### 10\. RFC-0009: Sistema de Inteligencia de Crecimiento (Growth Intelligence)

Responsable de maximizar la demanda calificada mediante la optimización de embudos y conversaciones en WhatsApp.**Moat Agéntico:**  Como visión de Fase 2, cada propiedad ATLAS se expone como un  **servidor MCP (Model Context Protocol)** , permitiendo que agentes externos (ChatGPT, Claude, Perplexity) descubran disponibilidad y amenities en tiempo real sin pasar por OTAs, habilitando la reserva directa agéntica.

#### 11\. RFC-0010: Capa de Invocación de Herramientas y Ejecución

Define la frontera de seguridad entre el razonamiento (probabilístico) y la ejecución (determinística).

* **Pipeline:**  IA Procesa →  **BDVL (Business Decision Validation Layer)**  Valida → Tool Dispatcher Enruta → Domain Service Ejecuta.  
* **BDVL:**  Actúa como un motor de políticas que clasifica acciones por riesgo. Acciones "Low" pueden automatizarse; acciones "Critical" requieren aprobación humana explícita.  
* **Seguridad:**  Validación obligatoria de esquemas JSON y registro exhaustivo en execution\_logs para auditoría técnica.

#### 12\. Matriz de Trazabilidad y Consistencia

Las especificaciones RFC 0001-0010 convergen en la  **North Star Metric: Horas operativas ahorradas por propiedad** . Cada componente arquitectónico ha sido diseñado para reducir la fricción del propietario, delegando la complejidad a la IA.**Infraestructura Tecnológica:**

* **Base de Datos:**  Supabase (PostgreSQL \+ pgvector) con RLS estricto.  
* **Modelos IA:**  Claude (Anthropic) como modelo L4 primario para razonamiento complejo.  
* **Orquestación:**  n8n para workflows asíncronos y Next.js para servicios de dominio.Este blueprint asegura que ATLAS escale de manera eficiente, manteniendo la integridad multi-tenant y la superioridad competitiva mediante inteligencia distribuida.


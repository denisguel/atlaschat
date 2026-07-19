### Especificación Maestra de Arquitectura: Proyecto ATLAS

Este documento constituye la especificación técnica definitiva y canónica de la arquitectura de ATLAS. Define los principios, estructuras y modelos operativos necesarios para transformar el software de hospitalidad tradicional en un  **Sistema Operativo AI-Native** .

#### 1\. Filosofía y Visión del Sistema (ARCH-000, ARCH-020)

ATLAS no es una aplicación con funciones de IA añadidas; es un  **Sistema Operativo AI-Native** . La distinción fundamental radica en el flujo de ejecución: mientras el software tradicional inserta la IA como un intermediario o una herramienta secundaria, en ATLAS el usuario interactúa directamente con la IA, la IA opera el software y el software actúa estrictamente como un motor de ejecución determinístico y persistente.**Misión:**  Maximizar la rentabilidad de alojamientos independientes mediante un sistema impulsado por IA que reduce la carga operativa y mejora continuamente las decisiones de negocio (AES-00).**Pilares Arquitectónicos:**

* **AI-Native:**  La IA es el núcleo de ejecución y el procesador de intención, no una característica periférica.  
* **Event-Driven (ADR-002):**  Comunicación basada en eventos de dominio inmutables para un desacoplamiento total entre agentes y servicios.  
* **Multi-Agent (ADR-004):**  Especialización de inteligencia mediante múltiples agentes lógicos con niveles de ejecución explícitos.  
* **Memoria Persistente por Brain (ADR-005 / ARCH-013):**  Conocimiento contextual separado del estado transaccional, organizado en dominios de "Cerebro".  
* **Human-in-the-loop por defecto:**  Las decisiones de alto impacto requieren supervisión humana para garantizar el control del negocio.  
* **Seguro por diseño (ARCH-028 / DOC-0031):**  Aislamiento multi-tenant estricto mediante RLS y guardrails de seguridad de IA integrados.  
* **Provider-aware por costo (ADR-011):**  Selección dinámica de modelos: Claude para razonamiento crítico y modelos gratuitos para tareas de bajo riesgo.  
* **Single Source of Truth (SSOT):**  PostgreSQL (Supabase) es la única fuente de verdad para el estado operativo del sistema.

#### 2\. Modelo de Inteligencia y Niveles de Ejecución (ARCH-021, ARCH-030, ADR-011)

ATLAS emplea un modelo de pirámide de ejecución para optimizar la latencia y el costo, escalando la complejidad del razonamiento solo cuando la tarea lo exige.

##### Niveles de Ejecución (Pyramid Model)

Nivel,Nombre,Descripción,Proveedor por Defecto,Ejemplo de Request  
L0,Determinístico,Reglas fijas y lógica de negocio pura.,Código (Sin IA),"""¿Cuál es el WiFi?"""  
L1,IA Simple,Clasificación y extracción de datos.,Groq/Gemini (Basado en costo/latencia),"Clasificar mensaje como ""Queja"""  
L2,IA Contextual,Conversación con contexto de propiedad.,Claude (Anthropic),"""¿Cómo llego al alojamiento?"""  
L3,IA Analítica,Razonamiento sobre pricing y revenue.,Claude (Anthropic),"""¿Qué precio sugieres para mañana?"""  
L4,IA Estratégica,Análisis multi-propiedad y Growth.,Claude (Anthropic),"""Optimiza la ocupación de la temporada"""  
*Nota: La selección del proveedor L1 se realiza bajo criterios operativos de costo/latencia al momento de la implementación, no como una decisión arquitectónica fija.***Principios Inmutables de la Arquitectura de Ejecución:**

1. **Simplicidad Inicial:**  Comenzar siempre con la ejecución más simple posible (L0/L1).  
2. **Escalada por Evidencia:**  Escalar niveles de IA solo si la evidencia de la tarea lo requiere.  
3. **Justificación de Costo:**  El valor de negocio debe justificar el costo del razonamiento avanzado.  
4. **Recuperación Selectiva:**  Nunca cargar todos los Brains; solo el contexto estrictamente necesario.  
5. **Agente Único por Defecto:**  La colaboración multi-agente es la excepción por complejidad, no la regla.  
6. **Planificación Explícita:**  El componente "Planner" no se invoca de forma automática ni gratuita.  
7. **Resultado de Negocio:**  La calidad del sistema se mide por el éxito del negocio, no por la sofisticación del prompt.

#### 3\. Arquitectura de Sistemas y Capas (ARCH-002, ARCH-003)

El sistema se estructura en 5 capas primarias con responsabilidades estrictas:

1. **Presentación:**  Dashboards administrativos, web app y motores de reserva.  
2. **Conversación:**  Interfaz de entrada/salida para canales como WhatsApp y Telegram. Normaliza el input.  
3. **Orquestación AI:**  Capa de razonamiento que interpreta intención, recupera contexto y selecciona la estrategia.  
4. **Servicios de Dominio:**  Ejecución determinística. Valida comandos y aplica reglas de negocio inmutables.  
5. **Infraestructura y Persistencia:**  Supabase (PostgreSQL), bus de eventos y almacenamiento de archivos.

##### Subsistemas Macro

##### Sistema de Conversación

Encargado de la gestión de canales externos. Captura el input del usuario, normaliza el mensaje y detecta el tipo de intención sin aplicar lógica de negocio.

##### Orquestación AI

El centro de control de inteligencia. Coordina la secuencia de ejecución, gestiona el presupuesto de tokens y rutea los modelos según el nivel de ejecución requerido.

##### Sistema de Agentes de Dominio

Unidades de razonamiento especializadas que actúan en ámbitos específicos (Revenue, Guest, etc.). No ejecutan acciones; producen propuestas estructuradas para los servicios.

##### Servicios de Dominio

El núcleo determinístico de ATLAS. Incluye los servicios de Reserva, Propiedad y Precios. Aseguran que el sistema se comporte de forma consistente y autoritativa.

##### Sistema de Memoria (Brains)

Subsistema de almacenamiento semántico y persistente. Separa el conocimiento contextual (Guest, Property, Growth) del estado operativo transaccional.

##### Sistema de Eventos e Infraestructura

La columna vertebral asíncrona. Gestiona el bus de eventos inmutables que comunica cambios de estado entre todos los componentes del sistema.

#### 4\. Sistema Multi-Agente (MAS) y Orquestación (ARCH-012, ARCH-014, ADR-004)

ATLAS implementa un sistema multi-agente  **lógico** , no físico. Durante el MVP, los agentes residen en el mismo proceso de backend para minimizar latencia, operando como componentes de software especializados.

##### Agentes de Dominio y Responsabilidades

* **Guest Agent:**  Comunicación con huéspedes, mantenimiento del tono de hospitalidad y FAQs.  
* **Reservation Agent:**  Razonamiento sobre disponibilidad, políticas de cancelación y modificaciones.  
* **Revenue Agent:**  Análisis de demanda de mercado, precios dinámicos y optimización de ingresos.  
* **Growth Agent:**  Inteligencia comercial, marketing y conversión de reservas directas.  
* **Operations Agent:**  Coordinación de limpieza, mantenimiento y preparación de unidades.  
* **Property Agent:**  Custodio del conocimiento operativo (reglas de la casa, manuales, amenidades).  
* **Knowledge Agent:**  Especializado en recuperación semántica y búsqueda avanzada en la base de conocimientos.

##### Componentes de la Capa de Orquestación AI

* **Resolver:**  Determina el nivel de ejecución (L0-L4) mínimo necesario.  
* **Context Builder:**  Ensambla el contexto recuperando datos de Brains y PostgreSQL (Ver ARCH-029).  
* **Model Router:**  Selecciona el proveedor (Claude vs Modelos Gratuitos) según el nivel y costo.  
* **Planner:**  Descompone peticiones complejas en pasos ejecutables para niveles L3/L4.  
* **Agent Router:**  Dirige la solicitud al agente o conjunto de agentes especializados.

#### 5\. Capa de Validación y Seguridad (ARCH-022, ARCH-028)

La  **Business Decision Validation Layer (BDVL)**  es el control de autoridad definitivo del sistema. Transforma a la IA de un ejecutor autónomo potencialmente peligroso en un sistema de toma de decisiones supervisado y seguro.**Regla Inmutable:**  Ninguna IA ejecuta directamente sobre servicios determinísticos sin pasar por la BDVL. La IA propone, el sistema valida y el servicio ejecuta.

##### Matriz de Clasificación de Riesgo

Riesgo,Ejemplos,Requisito de Aprobación  
Bajo,"FAQs, resúmenes, traducciones.",Ejecución automática.  
Medio,"Tareas de limpieza, notas de huéspedes.",Validación de política interna.  
Alto,"Ajustes de precio, emisión de cupones.",Requiere confirmación (User).  
Crítico,"Cancelación de reservas, reembolsos, borrado.",Aprobación humana obligatoria.

##### Matriz de Amenazas y Guardrails de IA

Amenaza,Guardrail Obligatorio  
Prompt Injection,"Separación de Roles (§4):  Instrucciones de sistema, contexto de negocio y contenido externo viajan en campos separados."  
Fuga de Datos (Multi-tenant),"Filtrado por property\_id en la capa de base de datos (PostgreSQL RLS), no en el prompt de la IA."  
Alucinación de Negocio,BDVL obligatorio para contrastar propuestas contra reglas de negocio inmutables.  
Escalada de Costo,Circuit breaker de presupuesto por organización y límites de profundidad de razonamiento.

#### 6\. Arquitectura de Datos, Eventos y Consistencia (ARCH-015, ARCH-006, ARCH-024, ADR-002)

ATLAS utiliza PostgreSQL como su  **Single Source of Truth (SSOT)** .

* **Modelo de Consistencia:**  Consistencia Fuerte para operaciones transaccionales y Consistencia Eventual para proyecciones y actualizaciones de memoria (Brains).  
* **Arquitectura EDA:**  Las comunicaciones entre componentes son asíncronas y se basan en eventos inmutables categorizados en:  **Domain**  (negocio),  **AI**  (razonamiento),  **Integration**  (sistemas externos) e  **Infrastructure**  (sistema).  
* **Transactional Outbox Pattern:**  Para garantizar la integridad, los eventos se escriben en una tabla "Outbox" dentro de la misma transacción de la base de datos operativa, asegurando que el evento solo se publique si el commit en PostgreSQL es exitoso.

#### 7\. Arquitectura de Memoria (Brains) y Recuperación de Contexto (ARCH-013, ARCH-029, ADR-005)

La memoria se organiza en dominios especializados que alimentan al Context Builder.

##### Dominios de Memoria

* **Guest Brain:**  Perfiles, preferencias e historial de interacción.  
* **Property Brain:**  Conocimiento operativo, manuales y reglas de cada propiedad.  
* **Revenue Brain:**  Insights históricos de precios y patrones de demanda.  
* **Growth Brain:**  Atribución de marketing y performance de conversión.**Política de Retención (v2.1):**  
* *Conversation Memory:*  Horas (volátil).  
* *Brains (Guest, Property, Growth):*  Indefinido (conocimiento duradero).  
* *Decision Graph:*  Registro inmutable de datos, reglas y agentes usados en cada decisión.**Principios Inmutables del Context Builder (ARCH-029):**  
1. **Nunca recuperar todo (**  ***Never retrieve everything***  **):**  La saturación de contexto degrada la calidad.  
2. **Recuperar solo lo necesario:**  Eficiencia máxima en la selección de datos.  
3. **La compresión precede al truncamiento:**  Priorizar la consolidación de hechos sobre el corte abrupto de texto.  
4. **El ranking precede a la generación:**  Clasificar la relevancia antes de entregar el dato a la IA.  
5. **El software recupera, la IA razona:**  La búsqueda es determinística; la IA solo procesa el resultado.

#### 8\. Interfaces Conversacionales y Externas (ARCH-008, ADR-003, ADR-003-A, ADR-007)

**WhatsApp First:**  La tesis central es que la conversación es la interfaz operativa principal. El dashboard web queda relegado a tareas administrativas complejas.

* **Telegram como Canal Interino (ADR-003-A):**  Durante la Fase 1, se utiliza Telegram para acelerar la validación sin las fricciones de aprobación de Meta, manteniendo la capa de abstracción lista para migrar a WhatsApp Business API.  
* **API Gateway:**  Punto de entrada único que gestiona autenticación vía  **Supabase Auth** . Cada petición debe resolver obligatoriamente un contexto de tenant\_id y property\_id.  
* **MCP-Ready (ADR-007):**  Las capacidades de ATLAS se exponen como herramientas estandarizadas siguiendo el  **Model Context Protocol** , permitiendo interoperabilidad futura con ecosistemas externos de agentes.

#### 9\. Modelo de Responsabilidad de Workflows (ARCH-026)

Frontera estricta entre el Backend (lógica) y n8n (orquestación asíncrona).

##### Responsabilidades: Backend vs. n8n

Permitido en el Backend (Core),Permitido en n8n (Workflows)  
"Calcular precios, descuentos y políticas.",Enviar notificaciones y recordatorios.  
Validar la integridad de reservas.,Sincronizar calendarios externos (iCal).  
Autorizar reembolsos y flujos financieros.,Programar tareas post-checkout (limpieza).  
Persistir estados de negocio (SSOT).,Reintentar integraciones externas fallidas.  
PROHIBIDO:  Ejecutar SQL o lógica de BD.,PROHIBIDO:  Ejecutar SQL desde automatización.  
PROHIBIDO:  Delegar decisiones de negocio.,PROHIBIDO:  Calcular precios condicionales.  
PROHIBIDO:  Orquestar esperas largas.,PROHIBIDO:  Tomar decisiones de IA en nodos.

#### 10\. Infraestructura, Despliegue y Escalabilidad (ARCH-010, ARCH-025)

**Modular Monolith:**  Se prioriza la ejecución de todos los componentes en un solo proceso para el MVP, evitando la complejidad y latencia de microservicios prematuros.

##### Estrategia de Escalabilidad por Fases

1. **Fase 1:**  Monolito modular con instancia única de PostgreSQL.  
2. **Fase 2:**  Implementación de réplicas de lectura.  
3. **Fase 3:**  Particionamiento de tablas por tenant.  
4. **Fase 4:**  Extracción de servicios críticos (ej. Messaging o Revenue) a servicios independientes.  
5. **Fase 5:**  Arquitectura geo-distribuida.**Stack Tecnológico MVP:**  Supabase (PostgreSQL/RLS), Next.js, n8n y despliegue en Vercel.

#### 11\. Hoja de Ruta e Implementación Actual (ADR-010, ARCH-018, ADR-013)

**Enfoque Fase 1:**  El alcance está reducido exclusivamente al  **Revenue Agent**  para validar la inteligencia de pricing en un entorno real con el cliente ancla  **LindaBay** .

* **Reescritura Estratégica (ADR-013):**  Se descarta la aplicación previa debido a la deuda técnica crítica por el hardcoding de Gemini y, fundamentalmente, por la falta de un modelo de aislamiento multi-tenant nativo (RLS) definido en DOC-0031. La reescritura sobre el modelo de  **DOC-0032**  garantiza la seguridad de los datos desde la base.  
* **Gaps Identificados (ARCH-020):**  Los documentos de máxima prioridad pendientes son la  *Data Model Specification*  final y la  *Testing Strategy*  específica para prevenir regresiones en el aislamiento de tenants (RLS).**Aprobado por:**  Arquitecto Principal de ATLAS  **Versión:**  2.0 (Basado en corpus ARCH/ADR)


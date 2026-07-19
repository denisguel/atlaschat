### Especificación Maestra de Arquitectura Consolidada: Proyecto ATLAS

ATLAS no es un software de gestión tradicional; es un  **AI-Native Operating System**  diseñado para redefinir la operación en la industria de la hospitalidad. Mientras que los sistemas convencionales operan bajo el modelo pasivo de  *Usuario \-\> Interfaz (UI) \-\> Backend* , ATLAS transiciona hacia un modelo de ejecución activa:  **Usuario \-\> AI \-\> Ejecución** . En este paradigma, la Inteligencia Artificial no es un complemento, sino el núcleo del sistema que interpreta intenciones, recupera conocimiento institucional a través de "Brains" y coordina la ejecución determinista de la lógica de negocio.

#### 1\. Principios Arquitectónicos y Filosóficos (ARCH-001)

La arquitectura de ATLAS se fundamenta en 20 principios críticos que rigen toda decisión técnica. Estos principios garantizan que el sistema sea inteligente, seguro y escalable.

##### Core AI & Intelligence

* **AI-Native:**  La IA es el núcleo. Si un agente puede resolver un problema, el sistema se diseña alrededor del agente, no de un formulario.  
* **Memory First:**  Toda interacción genera conocimiento inmutable. Nada se descarta; cada decisión alimenta la memoria a largo plazo.  
* **Context Before Prompt:**  Los LLM nunca responden al vacío. El Context Builder recupera datos de los Brains antes de cualquier inferencia.  
* **Stateless Agents:**  Los agentes son componentes lógicos sin estado. La persistencia reside exclusivamente en la base de datos y el Vector Store.  
* **Progressive Intelligence:**  El sistema evoluciona de asistir a sugerir, automatizar, optimizar y finalmente predecir.

##### Operatividad y Dominio

* **WhatsApp First:**  La conversación es la interfaz operativa primaria. El Dashboard es una herramienta administrativa y visual complementaria.  
* **API First:**  Toda funcionalidad debe exponerse mediante APIs internas. La interfaz nunca contiene lógica crítica.  
* **Automation First:**  Toda tarea repetitiva (pagos, instrucciones, reportes) debe ser un workflow automatizable.  
* **Single Source of Truth (SSOT):**  PostgreSQL es la única fuente de verdad operativa. La IA informa, pero la base de datos manda.  
* **Modularidad:**  Cada módulo (Revenue, Cleaning, Messaging) evoluciona con bajo acoplamiento.  
* **Hospitality Native:**  Cada función debe resolver un problema real de la hospitalidad, evitando generalismos.

##### Evolución, Seguridad y Eficiencia

* **Event Driven:**  El sistema reacciona a eventos inmutables, no a consultas constantes.  
* **Human in the Loop:**  La autonomía se define por niveles; las decisiones críticas requieren aprobación humana explícita.  
* **Escalabilidad Horizontal:**  Diseñado para crecer de una propiedad a miles sin rediseño arquitectónico.  
* **Cost Efficiency:**  Optimización de tokens y recursos serverless para maximizar el margen operativo.  
* **Security by Design:**  Row Level Security (RLS) y cifrado desde la concepción del dato.  
* **Explainability:**  Ninguna decisión de la IA es una "caja negra"; debe ser auditable y explicable para el propietario.  
* **Observability:**  Logs, trazas y métricas obligatorias en cada capa de ejecución.  
* **MVP First:**  Foco en valor medible (ingresos, reducción de carga operativa) antes que en elegancia técnica.  
* **Evolución Compatible:**  Garantía de compatibilidad mediante contratos de herramientas y APIs versionadas.

##### Checklist de Nueva Funcionalidad

Criterio,Verificación  
¿Respeta el principio AI-Native y puede ser resuelto por un agente?,  
¿Genera eventos inmutables para el Bus de Eventos?,  
¿Alimenta la memoria persistente del sistema (Brains)?,  
¿Es auditable y su lógica es explicable para el usuario final?,  
¿Aplica Row Level Security (RLS) para aislamiento de inquilinos?,  
¿Reduce mediblemente el trabajo operativo del propietario?,

#### 2\. Modelo de Arquitectura de Alto Nivel

##### 2.1 Capas del Sistema (ARCH-002)

ATLAS se organiza en cinco capas con una regla estricta de  **No-Bypass** : ninguna capa puede saltarse a la siguiente para garantizar la integridad y el desacoplamiento.

1. **Capa de Presentación:**  Interfaces externas (WhatsApp, Web App, Email) que capturan la intención del usuario.  
2. **Capa de Conversación:**  Normaliza los mensajes y adapta el canal hacia un modelo de intención común.  
3. **Capa de Orquestación AI:**  El cerebro del sistema. Incluye el Orchestrator, el Planner y el Context Builder.  
4. **Servicios de Dominio:**  Capa de ejecución determinista (Reservas, Precios, Tareas). No contiene lógica de IA.  
5. **Infraestructura y Persistencia:**  Supabase (Postgres), Almacenamiento de vectores y Bus de Eventos.**Standard Request Flow:**  Usuario \-\> Capa de Conversación \-\> Orquestador AI \-\> Context Builder (Retrieval) \-\> Agente de Dominio \-\> Business Decision Validation Layer (BDVL) \-\> Servicio de Dominio \-\> Transactional Outbox \-\> PostgreSQL \-\> Bus de Eventos \-\> Respuesta.

##### 2.2 Separación entre Lógica y Física (ARCH-0025)

Existe una distinción crítica entre la responsabilidad lógica y el despliegue físico. Para el MVP, ATLAS adopta un  **Modular Monolith**  para minimizar latencia y costos operativos.

* **Condiciones para la extracción de microservicios:**  Un módulo se extraerá solo bajo tres condiciones: 1\) Necesidad de cómputo específico (ej. clusters de inferencia GPU); 2\) Escalado independiente del bus de mensajería por volumen crítico; 3\) Requerimientos de despliegue geográfico diferenciado.

#### 3\. El Sistema de Inteligencia Multi-Agente (ARCH-014, RFC-0004)

##### 3.1 Orquestador y Planificador

El  **AI Orchestrator**  coordina el ciclo de vida de la solicitud. Realiza la clasificación de intención y selecciona el agente adecuado. El  **Planner**  es un componente de alto costo que se activa únicamente ante requerimientos complejos que cruzan dominios o requieren pasos múltiples (ej: "Cancela esta reserva, busca una alternativa y avísale al personal de limpieza"). Los agentes nunca se comunican entre sí; toda coordinación es mediada por el Orquestador para evitar dependencias circulares.

##### 3.2 Catálogo de Agentes Especializados

Agente,Dominio de Responsabilidad,Brain Primario,Herramientas (Tools)  
Guest,"Satisfacción, tono y FAQ.",Guest Brain,"Messaging, Recs Engine"  
Reservation,Disponibilidad y políticas.,Property Brain,"BookingTool, InventoryAPI"  
Revenue,Estrategia y rentabilidad.,Revenue Brain,"PricingTool, MarketAnalytics"  
Growth,Marketing y conversión.,Growth Brain,"CampaignTool, LeadGen"  
Operations,Limpieza y mantenimiento.,Property Brain,"TaskManager, MaintenanceAPI"  
Property,Reglas y documentación técnica.,Property Brain,KnowledgeSearch (RAG)  
Knowledge,Recuperación y síntesis.,Todos los Brains,"VectorSearch, Summarizer"

##### 3.3 Niveles de Ejecución AI (ARCH-0021)

Para optimizar latencia y costo, ATLAS utiliza una pirámide de ejecución:

1. **L0 (Determinista):**  Respuestas cacheadas o reglas fijas (ej. "Gracias"). Sin uso de LLM.  
2. **L1 (NLP Básico):**  Traducciones o tareas de un solo paso sin contexto histórico.  
3. **L2 (Contextual):**  Consultas que requieren recuperación de un solo Brain (ej. "¿Wifi?").  
4. **L3 (Multi-Agente):**  Tareas que requieren razonamiento entre dominios y validación de políticas.  
5. **L4 (Estratégico):**  Optimización de portafolio y análisis de tendencias a largo plazo (Ejecución asíncrona).

#### 4\. Estrategia de Memoria y Contexto (ARCH-013, ADR-005)

##### 4.1 Arquitectura de "Brains"

Los Brains separan el conocimiento semántico del estado transaccional.

* **Guest Brain:**  Historial, preferencias y sentimientos del huésped.  
* **Property Brain:**  Manuales, reglas de la casa y guías operativas.  
* **Revenue Brain:**  Inteligencia comercial, elasticidad de precios y tendencias de mercado.  **Regla de Oro:**  La memoria informa la decisión (probabilística), pero PostgreSQL mantiene el estado (determinista). La memoria nunca reemplaza al estado transaccional.

##### 4.2 Estrategia de Recuperación de Contexto (ARCH-0029)

El pipeline de recuperación sigue un orden estricto:

1. **Identificación:**  Resolución de entidades (Organización, Propiedad, Huésped).  
2. **Búsqueda Híbrida:**  Combinación de pgvector (semántica) con filtros SQL (metadatos) para asegurar el aislamiento (Tenancy).  
3. **Ranking:**  Clasificación por relevancia, recencia y confianza.  
4. **Compresión Semántica:**  Resumen de información redundante antes de la construcción del prompt para optimizar la ventana de contexto.

#### 5\. Gestión de Estado y Persistencia (ARCH-011, ARCH-024)

##### 5.1 Single Source of Truth (SSOT)

PostgreSQL (vía Supabase) es la única fuente de verdad. El sistema garantiza  **Consistencia Fuerte**  para transacciones de negocio (reservas, pagos) y  **Consistencia Eventual**  para proyecciones de lectura, analíticas y actualizaciones de Brains.

##### 5.2 Modelo de Datos y Multitenencia

Aislamiento físico-lógico mediante la jerarquía: Organization \-\> Property \-\> Unit \-\> Reservation. Se imponen UUIDs para todos los registros y políticas de  **Row Level Security (RLS)**  para garantizar que ninguna organización acceda a datos ajenos, ni siquiera a través de la IA.

#### 6\. Comunicación y Eventos (ADR-002, RFC-0005)

##### 6.1 Arquitectura Event-Driven

El bus de eventos desacopla los servicios. Categorías:  **Domain**  (cambios de estado),  **AI**  (razonamiento),  **Integration**  (mensajes externos) e  **Infrastructure**  (logs operativos).

##### 6.2 Patrón Transactional Outbox

Para evitar inconsistencias entre la DB y el Bus de Eventos, se garantiza la  **Atomicidad** :

1. Dentro de una  **única transacción de Postgres** , se actualizan las tablas de negocio y se inserta el evento en la tabla outbox.  
2. Al hacer  **Commit** , ambos registros se consolidan.  
3. Un Publisher asíncrono lee la tabla outbox y distribuye al bus. Si el Publisher falla, el estado en la DB sigue siendo la verdad absoluta y el evento se reintenta.

#### 7\. Capa de Validación y Seguridad (ARCH-022, RFC-0010)

##### 7.1 Business Decision Validation Layer (BDVL)

La IA es  **probabilística**  (puede alucinar); el BDVL es  **determinista**  (código basado en reglas y políticas). Ninguna acción propuesta por un agente llega a los servicios de dominio sin pasar por este guardián que valida semántica, límites financieros y políticas de negocio.

##### 7.2 Clasificación de Riesgo y Aprobación

Nivel de Riesgo,Acción Ejemplo,Requisito de Aprobación  
Bajo,"FAQ, redacción de captions.",Automático.  
Medio,Crear tarea de limpieza.,Validación de política automática.  
Alto,Ajustar precio de una noche.,Confirmación de usuario sugerida.  
Crítico,"Cancelar reserva, Reembolso.",Aprobación humana obligatoria.

#### 8\. Interfaces y Estrategia UX (ARCH-023, ADR-003)

##### 8.1 WhatsApp-First

La interfaz primaria es conversacional para normalizar la entrada de datos mediante  **Conversation Adapters** , permitiendo que el backend sea agnóstico al canal. Esto reduce la fricción de adopción en mercados donde la movilidad es la norma.

##### 8.2 Matriz de Interacción Híbrida

Tarea,Interfaz Recomendada,Justificación  
Check-in rápido / FAQ,Conversacional,"Baja densidad de datos, alta urgencia."  
Gestión de una reserva,Conversacional,"Flujo lineal, ejecución simple."  
Calendario de ocupación,Dashboard,Alta densidad de información espacial.  
Reporte de Revenue Anual,Dashboard,Necesidad de visualización comparativa.

#### 9\. Infraestructura y Escalabilidad (ARCH-010, ARCH-017)

##### 9.1 Stack Tecnológico MVP

* **Backend/Frontend:**  Next.js (Modular Monolith).  
* **Persistencia:**  Supabase (PostgreSQL \+ pgvector).  
* **Automatización:**  n8n (Orquestación de efectos secundarios asíncronos).  
* **Modelos LLM:**  GPT-4o, Claude 3.5, Gemini 1.5 (Ruteo por complejidad).

##### 9.2 Optimización de Costos AI

Se aplica ruteo inteligente: modelos pequeños para L1/L2 y modelos de alto razonamiento para L3/L4. El uso de  **Caché Semántico**  evita llamadas redundantes al LLM para preguntas frecuentes ya procesadas.

#### 10\. Evolución y Roadmaps (ARCH-018, ARCH-019)

##### 10.1 Preparación para MCP y Event Sourcing

ATLAS es  **MCP-Ready** ; todas las herramientas de los agentes siguen contratos de esquemas compatibles con el Model Context Protocol. Es  **Event Sourcing Ready**  mediante el registro inmutable de eventos, permitiendo reconstruir estados históricos sin rediseñar el dominio.

##### 10.2 Roadmap de Inteligencia Progresiva

* **Fase 0:**  Foundation (Repo, CI/CD, Supabase Setup).  
* **Fase 1:**  Core Platform (Organizaciones, Unidades, Reservas).  
* **Fase 2:**  AI Foundation (Orquestador, Context Builder).  
* **Fase 3:**  Guest Operations (WhatsApp Integration, Guest Agent).  
* **Fase 4:**  Revenue Intelligence (Revenue Brain, Pricing Recs).  
* **Fase 5:**  Growth Intelligence (Growth Brain, Lead Gen).  
* **Fase 6:**  Workflow Automation (n8n Integration, Reminders).  
* **Fase 7:**  Booking Engine (Direct Sales channel).  
* **Fase 8:**  Operational Intelligence (Task Tracking, Maintenance).  
* **Fase 9:**  Analytics (Executive Dashboards, KPIs).  
* **Fase 10:**  Production Hardening (Security Review, Scale Testing).


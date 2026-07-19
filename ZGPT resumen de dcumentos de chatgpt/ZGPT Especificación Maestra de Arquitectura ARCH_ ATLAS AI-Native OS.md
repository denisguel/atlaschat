### Especificación Maestra de Arquitectura ARCH: ATLAS AI-Native OS

#### 1\. Introducción y Propósito del Sistema

ATLAS se define como un  **AI-Native Hospitality Operating System** . Representa un cambio de paradigma fundamental: no es "software habilitado para IA", sino  **software operado por IA** . En esta arquitectura, la IA no es un módulo periférico, sino el núcleo ontológico del sistema; el backend se transforma en un motor de ejecución determinista guiado por razonamiento inteligente (ARCH-000).

##### Objetivos Fundamentales (G1-G6)

* **G1 \- Interfaz Conversacional Total:**  Operación íntegra mediante interfaces conversacionales, con WhatsApp como canal primario (ADR-003).  
* **G2 \- Ejecución Autónoma:**  Capacidad de agentes para ejecutar flujos de trabajo completos bajo niveles de autonomía configurables.  
* **G3 \- Separación de Inteligencia y Ejecución:**  El razonamiento reside en los agentes; la ejecución en los servicios; la persistencia en el SSOT.  
* **G4 \- Modularidad Estricta:**  Capacidades mayores con evolución independiente y contratos publicados (ARCH-002).  
* **G5 \- Orientación a Eventos:**  Comunicación asíncrona mediante eventos de dominio inmutables (ADR-002).  
* **G6 \- Escalabilidad Horizontal:**  Crecimiento en propiedades, organizaciones e idiomas sin rediseño estructural (ARCH-010).

#### 2\. Principios Fundamentales (Los 20 Pilares)

La arquitectura de ATLAS se rige por 20 principios inmutables (01\_PRODUCT\_PRINCIPLES.md).| Principio | Descripción | Aplicación Arquitectónica || \------ | \------ | \------ || **1\. AI Native** | La IA es el núcleo, no un módulo. | El diseño se centra en el agente, no en pantallas. || **2\. Memory First** | Toda interacción genera conocimiento. | Alimentación constante de los "Brains" (pgvector). || **3\. Context Before Prompt** | Ningún LLM responde sin contexto. | Uso obligatorio del Context Builder (ARCH-0029). || **4\. Event Driven** | Todo cambio genera un evento. | Reacción asíncrona a cambios de estado. || **5\. Automation First** | Tareas repetitivas deben ser workflows. | Eliminación de clics manuales vía orquestación n8n. || **6\. Human in the Loop** | Niveles de autonomía definidos (1-3). | Capa de aprobación obligatoria según riesgo (ARCH-0022). || **7\. WhatsApp First** | Interfaz operativa primaria. | Adaptadores de conversación normalizados (ADR-003). || **8\. API First** | Funcionalidad vía APIs internas. | Separación total entre interfaz y lógica crítica. || **9\. Stateless Agents** | Los agentes no guardan estado. | Persistencia exclusiva en Postgres/Vector Stores. || **10\. Single Source of Truth** | Un único origen oficial para cada dato. | PostgreSQL como autoridad operativa definitiva (SSOT). || **11\. Modularidad** | Evolución independiente de módulos. | Bajo acoplamiento entre Revenue, Operaciones, etc. || **12\. Escala Horizontal** | Crecimiento multi-inquilino. | Aislamiento (Multi-tenancy) mediante RLS. || **13\. Cost Efficiency** | Minimización de costos en MVP. | Uso de modelos económicos (Model Routing \- ARCH-0027). || **14\. Security by Design** | Seguridad desde la concepción. | Auditoría total y cifrado en tránsito/reposo. || **15\. Explainability** | Decisiones IA deben ser explicables. | Trazabilidad del razonamiento para el propietario. || **16\. Observability** | Telemetría por defecto. | Logs, métricas y trazas vinculadas por Request ID. || **17\. Progressive Intelligence** | Aprendizaje evolutivo. | Fases: Asistencia → Sugerencia → Automatización. || **18\. MVP First** | Valor de negocio sobre elegancia. | Enfoque en ingresos y reducción de carga operativa. || **19\. Hospitality Native** | Valor específico para el sector. | Funcionalidades diseñadas para alojamientos reales. || **20\. Evolución Compatible** | Compatibilidad entre versiones. | Uso de ADRs para documentar rupturas inevitables. |  
**Nota de Invariante:**  Cualquier implementación que diverja de estos principios se considera una arquitectura distinta. El incumplimiento de estos pilares invalida la integridad del sistema ATLAS (ARCH-000).

#### 3\. Modelo Arquitectónico de 5 Capas

ATLAS emplea una estructura de capas con  **Estrictez de Capas (Layered Strictness)** : ninguna capa puede saltarse la inmediata inferior para interactuar con el sistema (ARCH-002).

1. **Capa de Presentación:**  Web App (Next.js) y Admin Dashboard. Optimizada para visualización densa (calendarios, dashboards de Revenue) donde la interfaz conversacional es ineficiente (ARCH-0023).  
2. **Capa de Conversación:**  Adaptadores (WhatsApp primary). Captura intención, normaliza input y delega al Orquestador. No contiene lógica de negocio ni persistencia.  
3. **Capa de Orquestación de IA:**  El "Cerebro". Incluye el  **Orchestrator Agent**  (ruteo),  **Context Builder**  (ensamblado de memoria) y  **Tool Selector**  (mapeo a servicios).  
4. **Capa de Servicios de Dominio:**  Ejecución determinista de lógica de negocio (Reservas, Precios). No contiene lógica de IA. Es la encargada de emitir eventos de dominio tras validaciones.  
5. **Capa de Persistencia e Infraestructura:**  PostgreSQL (Supabase) como SSOT. Incluye  **pgvector**  para memoria semántica, n8n para orquestación de efectos secundarios (notificaciones, sync externo) y el Bus de Eventos.

#### 4\. Niveles de Ejecución de Inteligencia (L0 \- L4)

Para optimizar latencia y costos, ATLAS emplea una pirámide adaptativa de ejecución (ARCH-0021).

##### Tabla Comparativa de Niveles

Nivel,Descripción,Complejidad,Latencia,Caso de Uso Típico  
L0,Reglas/Plantillas,Casi cero,\< 100ms,"Saludos, ""¿Cuál es el WiFi?"", FAQs estáticas."  
L1,Prompt Simple,Baja,\< 1s,"Traducciones, formato de mensajes, captions."  
L2,Agente Único,Media,1s \- 3s,"Consulta de una reserva, estado de limpieza."  
L3,Multi-Agente / Planner,Alta,3s \- 10s,"""Mover reserva y recalcular precio""."  
L4,Estratégico,Muy Alta,Asíncrona,Análisis de competencia y forecast anual.  
**Flujo de Escalado:**  El sistema inicia en L0 (Normalizador/Regex). Si requiere lenguaje natural pero no contexto, escala a L1. Si requiere datos de un Brain (ej. Guest Brain), escala a L2. Ante problemas cruzados (ej. Revenue \+ Operaciones), se activa el Planner en L3.

#### 5\. Sistema Multi-Agente (Especialización de Dominio)

ATLAS utiliza una  **Arquitectura Multi-Agente Lógica**  (ARCH-0025): los agentes son componentes de software especializados dentro del mismo proceso modular, minimizando latencia y sobrecostos de infraestructura microservicial.

##### Fichas Técnicas de Agentes (RFC-0004)

* **Guest Agent:**  Misión: Satisfacción y comunicación hospitalaria. Herramientas: Messaging Tool, FAQ.  
* **Reservation Agent:**  Misión: Gestión de disponibilidad y políticas. Herramientas: Booking/Availability Tools.  
* **Revenue Agent:**  Misión: Maximizar rentabilidad (RevPAR). Herramientas: Pricing Tool, Demand Forecast.  
* **Growth Agent:**  Misión: Conversión y marketing. Herramientas: Campaign/Content Tools.  
* **Operations Agent:**  Misión: Optimización operativa. Herramientas: Maintenance Tool, Cleaning Tool, Task Tool.  
* **Property Agent:**  Misión: Conocimiento del activo. Herramientas: Amenities Guide, House Rules.  
* **Knowledge Agent:**  Misión: Recuperación semántica y atribución de fuentes. Herramientas: Vector Search, Document Summarizer.**Regla de Oro:**  Los agentes nunca se comunican directamente. Toda coordinación es mediada por el  **Orquestador**  para evitar dependencias circulares y crecimiento descontrolado del contexto (ARCH-0025).

#### 6\. Arquitectura de Memoria (Los Brains)

La memoria es una capa de recuperación determinista, no un estado operativo (ARCH-0014).

* **Guest Brain:**  Historial, preferencias, patrones de lenguaje y LTV.  
* **Property Brain:**  Manuales, reglas de la casa, amenities y mantenimiento.  
* **Growth Brain:**  Inteligencia comercial, histórico de precios y rendimiento de campañas.**Implementación Física:**  El  **Estado Operativo**  reside en PostgreSQL (ACID). El  **Conocimiento Semántico**  reside en  **pgvector**  (embeddings). El ciclo de vida (ARCH-0014) comprende:  **Extracción**  (vía eventos),  **Consolidación**  (resúmenes para ahorro de tokens) y  **Recuperación**  (vía Context Builder).

#### 7\. Consistencia de Estado y Arquitectura de Eventos

PostgreSQL es la  **Única Fuente de Verdad (SSOT)** . Los eventos son hechos históricos inmutables (ARCH-0024).

##### Reglas de Persistencia y Eventos

* **Transactional Outbox Pattern:**  Los eventos se registran en una tabla outbox dentro de la misma transacción de base de datos que el cambio de estado. Solo tras un  **commit exitoso**  en PostgreSQL, el publicador envía el evento al bus (ADR-002).  
* **Trazabilidad Total:**  Cada evento debe contener obligatoriamente event\_id, correlation\_id (para rastrear la petición original) y causation\_id (para identificar qué evento disparó el actual), asegurando un sistema "Event Sourcing Ready" (ADR-009).  
* **Anti-patrones Prohibidos:**  Publicar eventos antes del commit; ejecutar lógica de negocio en suscriptores; usar eventos para definir la verdad operativa.

#### 8\. Capa de Validación de Decisiones de Negocio (BDVL)

El  **BDVL**  (ARCH-0022) actúa como un cortafuegos entre la IA (probabilística) y los Servicios de Dominio (deterministas).

##### Clasificación de Riesgo y Acciones

* **Bajo (FAQs, Captions):**  Ejecución automática.  
* **Medio (Notas, Tareas):**  Validación contra política de negocio.  
* **Alto (Precios, Disponibilidad):**  Confirmación requerida por el usuario.  
* **Crítico (Reembolsos, Cancelaciones, Borrado):**   **Aprobación Humana Obligatoria y Hard-coded** . El sistema no permitirá la ejecución sin un flag de autorización humana persistido en la transacción.**Prioridad de Confianza:**  El  **Riesgo de Negocio**  siempre invalida el Score de Confianza de la IA. Una confianza del 99% en una cancelación "Crítica" sigue requiriendo intervención humana.

#### 9\. Estrategia de Recuperación de Contexto (Context Builder)

El Context Builder (ARCH-0029) transforma la recuperación de datos en un proceso de software determinista.

##### Los 5 Principios de Recuperación

1. **Nunca recuperar todo:**  Selección basada en relevancia y presupuesto de tokens.  
2. **Recuperar solo lo necesario:**  Filtrado estricto por entidad (Org/Propiedad).  
3. **Compresión antes que truncamiento:**  Resumir historiales en lugar de cortarlos.  
4. **Ranking antes que generación:**  Clasificar información por prioridad de negocio.  
5. **Software recupera, IA razona:**  La IA no debe "buscar" datos, debe recibirlos listos para procesar.

#### 10\. Infraestructura MVP y Escalabilidad

ATLAS se despliega como un  **Monolito Modular**  (ARCH-0025) para optimizar latencia y simplicidad operativa inicial.

* **Stack:**  Next.js (Server/Edge), Supabase (Postgres \+ Auth), n8n (Workflows).  
* **Responsabilidad de n8n (ARCH-0026):**  n8n orquestará exclusivamente  **efectos secundarios**  (notificaciones, sincronizaciones externas).  **Nunca**  debe contener lógica de negocio crítica o decisiones de estado.  
* **Optimización de Costos (ARCH-0027):**  
* **Caché Semántico:**  Reutilización de respuestas para FAQs idénticas.  
* **Model Routing:**  Uso de modelos ligeros (L1/L2) para el 80% de las peticiones.  
* **Minimización de Contexto:**  Filtrado por metadatos antes de la búsqueda vectorial.

#### 11\. Hoja de Ruta y Criterios de Éxito

##### Evolución de la Inteligencia (ARCH-0019)

1. **Asistente:**  Reactivo, resuelve dudas.  
2. **Asesor:**  Proactivo, sugiere cambios de precio (Revenue Intelligence).  
3. **Operador:**  Autónomo en flujos validados (Check-in/out).  
4. **Socio Estratégico:**  Optimización de portafolio y forecast de mercado.

##### Criterios de Éxito Arquitectónico (ARCH-0018)

* **Consistencia:**  100% transaccional (Postgres First).  
* **Integridad:**  Cero eventos perdidos gracias al Outbox Pattern.  
* **Latencia:**  Respuesta conversacional \< 3s en L1/L2.  
* **Aislamiento:**  Multi-tenancy estricto a nivel de base de datos (RLS).


### ATLAS: Compendio de Especificaciones Técnicas y Manual de Implementación (DOC)

ATLAS se define como un sistema operativo de hospitalidad "AI-Native", una distinción fundamental donde la Inteligencia Artificial no es un módulo periférico, sino el núcleo del diseño sistémico. A diferencia del software tradicional o las aplicaciones "AI-enabled" que simplemente insertan un LLM entre la interfaz y el backend, ATLAS propone un cambio de paradigma:  **"El usuario interactúa con la IA. La IA opera el software. El software se convierte en un motor de ejecución"** . Este compendio detalla la arquitectura modular y los protocolos deterministas que permiten transformar el razonamiento probabilístico en operaciones hoteleras de alta fiabilidad.

#### 1\. Filosofía y Principios Fundamentales

Basado en el canon de "01\_PRODUCT\_PRINCIPLES.md", el sistema se rige por 20 principios inmutables:

1. **AI Native:**  La IA constituye el núcleo del sistema y no un añadido secundario. Toda nueva funcionalidad debe diseñarse bajo la premisa de que un agente, y no un formulario, es el ejecutor primario.  
2. **Memory First:**  Toda interacción genera conocimiento que debe ser preservado para alimentar decisiones futuras. Nunca se descarta información útil, ya que esta constituye el activo intelectual del sistema.  
3. **Context Before Prompt:**  Los modelos de lenguaje nunca responden basándose únicamente en el prompt recibido. La calidad de la respuesta está estrictamente ligada al contexto enriquecido provisto por los "Brains".  
4. **Event Driven:**  Todo cambio importante de estado genera un evento inmutable en el sistema. Los agentes reaccionan a estos hechos históricos de forma asíncrona, eliminando la necesidad de consultas permanentes.  
5. **Automation First:**  Las tareas repetitivas se identifican sistemáticamente para ser convertidas en flujos de trabajo automatizados. Si un proceso requiere múltiples clics redundantes, es candidato a la orquestación por agentes.  
6. **Human in the Loop:**  La automatización posee niveles de autonomía granulares que garantizan la supervisión humana según el riesgo. Cada workflow define explícitamente si requiere aprobación manual o ejecución autónoma.  
7. **WhatsApp First:**  WhatsApp constituye la interfaz operativa primaria para el mercado de LATAM por su nula curva de aprendizaje. El dashboard web se relega a tareas de configuración y visualización de datos densos.  
8. **API First:**  Toda funcionalidad debe estar disponible mediante APIs internas para garantizar el desacoplamiento. La interfaz nunca debe contener lógica de negocio crítica, actuando solo como capa de presentación.  
9. **Stateless Agents:**  Los agentes no almacenan memoria propia y son componentes puramente lógicos. Toda la persistencia reside en PostgreSQL para garantizar la consistencia y el reinicio de procesos sin pérdida de estado.  
10. **Single Source of Truth:**  Cada dato posee un único origen oficial para evitar inconsistencias operativas. Por ejemplo, la disponibilidad reside únicamente en el calendario interno y no se duplica en memorias volátiles.  
11. **Modularidad:**  El sistema se compone de módulos independientes con bajo acoplamiento que evolucionan por separado. Esto permite actualizar el motor de Revenue sin afectar la capa de mensajería o mantenimiento.  
12. **Escalabilidad Horizontal:**  La arquitectura soporta el crecimiento desde una propiedad individual hasta miles de unidades sin rediseño. El aislamiento lógico ( *Multi-tenancy* ) garantiza que cada organización opere en su propio silo de datos.  
13. **Cost Efficiency:**  El desarrollo prioriza el uso de infraestructura bajo demanda y optimización de tokens para maximizar el margen bruto. Se evita la sobreingeniería, escalando recursos solo cuando existen cuellos de botella medibles.  
14. **Security by Design:**  La seguridad y el cifrado se integran desde la concepción de cada servicio. El acceso se rige por el principio de mínimo privilegio y auditoría completa de cada acción ejecutada.  
15. **Explainability:**  Toda decisión tomada por la IA debe ser explicable y transparente para el propietario del negocio. Se prohíben los modelos de "caja negra", exigiendo razonamiento lógico en cada recomendación automática.  
16. **Observability:**  Cada proceso genera logs, métricas y trazas para permitir una auditoría constante. La observabilidad es obligatoria para garantizar la salud de la orquestación y la detección de anomalías.  
17. **Progressive Intelligence:**  La autonomía del sistema se adquiere de forma incremental mediante fases de aprendizaje supervisado. El sistema evoluciona desde la asistencia básica hasta la capacidad predictiva estratégica.  
18. **MVP First:**  Solo se desarrollan capacidades que aumenten ingresos o reduzcan carga operativa de forma inmediata. Se evita el desarrollo de funcionalidades por anticipado que no validen el modelo de negocio.  
19. **Hospitality Native:**  Todas las decisiones técnicas responden a necesidades específicas y reales del sector de la hospitalidad. No se construyen abstracciones genéricas que no aporten valor directo al propietario del alojamiento.  
20. **Evolución Compatible:**  Las nuevas versiones mantienen compatibilidad con los modelos de datos y flujos de trabajo existentes. Cualquier ruptura inevitable de compatibilidad debe documentarse rigurosamente mediante un ADR.

##### Tabla de Inteligencia Progresiva (Principio 17\)

Fase,Nivel de Autonomía,Descripción del Comportamiento  
Fase 1,Asiste,La IA ayuda al humano a realizar tareas manuales mediante herramientas de eficiencia.  
Fase 2,Sugiere,El sistema propone acciones proactivas basadas en datos que requieren aprobación humana.  
Fase 3,Automatiza,Ejecución automática de tareas repetitivas bajo reglas de negocio y políticas predefinidas.  
Fase 4,Optimiza,La IA ajusta parámetros operativos de forma autónoma para mejorar KPIs específicos (RevPAR).  
Fase 5,Predice,El sistema anticipa escenarios futuros y prepara la operación antes de que ocurra la demanda.

#### 2\. Arquitectura de Alto Nivel y Capas del Sistema

ATLAS se estructura en 5 capas primarias con responsabilidades estrictas para garantizar el desacoplamiento y la integridad:

* **Capa de Conversación:**  Actúa como el adaptador de canal (WhatsApp, Web, Email) encargado de capturar la intención, normalizar el input y renderizar las respuestas sin poseer lógica de negocio.  
* **Capa de Orquestación de IA:**  Funciona como el cerebro central que interpreta la intención, recupera contexto mediante el  *Context Builder*  y selecciona al agente de dominio adecuado para la tarea.  
* **Capa de Agentes de Dominio:**  Unidades de razonamiento especializado (Revenue, Ops, etc.) que producen planes de acción estructurados basados en el contexto recuperado de los Brains.  
* **Capa de Servicios de Dominio:**  Es la capa de ejecución determinista encargada de validar comandos, aplicar reglas de negocio inmutables y persistir el estado en la base de datos.  
* **Capa de Persistencia e Infraestructura:**  Gestiona la "Fuente de Verdad" en PostgreSQL, el almacenamiento de objetos y la orquestación de efectos secundarios asíncronos mediante el bus de eventos.

##### Modelo de Interacción de Componentes

* **Flujo Estándar (Request/Response):**  
* Usuario → Capa de Conversación (Normalización) → Orquestador de IA (Interpretación) → Context Builder (Enriquecimiento) → Agente de Dominio (Razonamiento) → Servicio de Dominio (Ejecución/Persistencia) → Generación de Respuesta.  
* **Flujo de Eventos (Asíncrono):**  
* Servicio de Dominio → Commit en PostgreSQL → Tabla Outbox → Publicador de Eventos → Event Bus → Suscriptores (Notificaciones, n8n, Actualización de Brains).

##### Restricciones Arquitectónicas Clave (Key Architectural Constraints)

Se prohíbe el acceso directo de la UI a la base de datos para preservar el aislamiento. Los agentes tienen prohibido persistir estado interno ( *Stateless* ); toda la memoria debe ser externa. Se exige el desacoplamiento total entre agentes: la comunicación entre ellos es mediada exclusivamente por el Orquestador o el Bus de Eventos. Finalmente, toda acción sobre el estado del negocio debe pasar obligatoriamente por un Servicio de Dominio determinista que garantice la integridad de los datos.

#### 3\. Orquestación de IA y Sistema Multi-Agente

ATLAS implementa un  **Multi-Agente Lógico** , donde los agentes residen en el mismo proceso backend para minimizar la latencia y complejidad operativa, diferenciándose de un modelo físico de microservicios.

##### Directorio de Agentes (Misiones según RFC-0004)

* **Guest Agent:**  Maximizar la satisfacción del huésped mediante conversaciones naturales, resolución de dudas y recomendaciones personalizadas, manteniendo siempre el tono de hospitalidad.  
* **Reservation Agent:**  Garantizar la integridad del ciclo de reserva, razonando sobre disponibilidad, políticas de cancelación y modificaciones complejas de estancias de forma determinista.  
* **Revenue Agent:**  Maximizar la rentabilidad a largo plazo mediante el análisis de demanda, elasticidad de precios y detección proactiva de oportunidades de mercado.  
* **Growth Agent:**  Optimizar la conversión comercial y el marketing directo, identificando fuentes de leads y recomendando contenido para aumentar el tráfico directo.  
* **Operations Agent:**  Coordinar la logística de la propiedad (limpieza y mantenimiento), priorizando tareas operativas según la urgencia y el estado de los check-ins.  
* **Property Agent:**  Especialista en el conocimiento profundo de la infraestructura,Amenities y reglas de la casa, actuando como la fuente de consulta para dudas operativas específicas.  
* **Knowledge Agent:**  Dedicado exclusivamente a la recuperación semántica y búsqueda de documentación técnica, garantizando que la información entregada tenga atribución directa a la fuente.

##### Niveles de Ejecución de IA (AI Execution Levels)

Nivel,Complejidad,Latencia,Ejemplos de Uso  
L0,Nula (Reglas/Regex),\< 500ms,"Respuestas automáticas (WiFi, ubicación), agradecimientos simples."  
L1,Baja (Prompt Directo),1-2s,Intención \+ Prompt sin contexto externo; traducciones o FAQs generales.  
L2,Media (Agente \+ DB),2-3s,"Consultas de estado de reserva, disponibilidad actual o historial de pagos."  
L3,Alta (Multi-Agente),3-5s,Cambios de fechas con re-cotización y análisis de impacto en Revenue.  
L4,Crítica (Planner),\> 5s,Análisis estratégicos de portafolio y simulaciones de demanda a largo plazo.

#### 4\. Arquitectura de Datos y Modelo de Consistencia

**PostgreSQL es la Única Fuente de Verdad (SSOT)** . ATLAS rechaza el uso de eventos como verdad operativa; el estado de las reservas y pagos debe ser gestionado mediante transacciones  **ACID**  para garantizar la integridad financiera.

##### Categorías de Datos y Residencia

1. **Datos Operacionales:**  Estado actual del negocio (Reservas, Unidades) en PostgreSQL.  
2. **Datos de Configuración:**  Políticas de organización y permisos (RLS) en PostgreSQL.  
3. **Datos Analíticos:**  Proyecciones y métricas de rendimiento en Proyecciones/Read Models de PostgreSQL.  
4. **Conocimiento Semántico:**  Embeddings y memorias históricas almacenadas en Vector Store (pgvector).

##### Patrón Transactional Outbox (Consistencia Fuerte)

Para evitar la desincronización entre la base de datos y los sistemas asíncronos, se sigue la secuencia: Inicio Transacción → Update Tablas de Negocio → Insert en Tabla Outbox → Atomic Commit (PostgreSQL) → Publicador Asíncrono → Event Bus.

#### 5\. El Sistema de Memoria (Brains)

La arquitectura de "Brains" separa el conocimiento histórico de la lógica de ejecución, permitiendo una personalización persistente.

##### Dominios de Memoria

* **Guest Brain:**  Almacena perfiles, preferencias de comunicación, historial de comportamiento y requerimientos específicos de cada huésped.  
* **Property Brain:**  Contiene el manual operativo, reglas de la casa, inventario de amenities e historial de incidencias de mantenimiento de la propiedad.  
* **Revenue Brain:**  Preserva el conocimiento analítico sobre patrones de demanda, efectividad de precios históricos y tendencias de mercado local.

##### Reglas de Conformidad (Memory Rules)

La IA tiene prohibido escribir directamente en los Brains. Toda actualización de memoria debe ser gatillada por eventos de negocio validados. El acceso se realiza exclusivamente a través del  **Context Builder** , que jerarquiza la información para evitar el desbordamiento de la ventana de contexto del LLM.

#### 6\. Capa de Validación y Ejecución (BDVL & Tools)

La  **Business Decision Validation Layer (BDVL)**  es el filtro crítico entre el razonamiento probabilístico de la IA y la ejecución determinista.

##### Business Confidence vs. AI Confidence

Un agente puede tener un 99% de confianza en que el usuario quiere un reembolso, pero si el riesgo financiero es alto, la  **BDVL**  anula la decisión de la IA y exige aprobación humana. El riesgo de negocio siempre tiene prioridad sobre la confianza estadística del modelo.

##### Clasificación de Riesgos

Nivel de Riesgo,Ejemplo,Requisito de Aprobación  
Bajo,"FAQ, traducción.",Ejecución automática inmediata.  
Medio,Crear tarea de limpieza.,Validación contra reglas de negocio.  
Alto,Cambiar precio/fechas.,Confirmación explícita del usuario.  
Crítico,"Reembolso, cancelación.",Aprobación humana explícita obligatoria.

#### 7\. Interfaz Conversacional y Estrategia UX

ATLAS adopta una estrategia  **"WhatsApp First"**  para el mercado de LATAM, capitalizando la adopción masiva y eliminando la fricción de descarga de aplicaciones tradicionales para propietarios móviles.

##### Matriz de Interacción

Tarea,Canal Preferido,Justificación,Ejemplo  
Consultas / Comandos,Conversación,Velocidad y conveniencia.,"""¿Hay check-ins hoy?"""  
Calendario / Ocupación,Dashboard,Superioridad visual espacial.,Ver disponibilidad de julio.  
Reportes Financieros,Dashboard,Alta densidad de información.,Comparativa de RevPAR anual.  
Configuración,Configuración,Tareas de gobernanza infrecuentes.,Integrar motor de pagos.

#### 8\. Infraestructura, Despliegue y Automatización

El sistema se despliega como un  **Monolito Modular**  en el MVP. Esta decisión minimiza la latencia de red entre agentes y simplifica drásticamente el  *debugging*  y la orquestación inicial sin sacrificar el desacoplamiento lógico.

* **Stack Tecnológico:**  Next.js (Serverless/Vercel), Supabase (Postgres \+ RLS \+ Auth), n8n.  
* **Responsabilidades de n8n:**  Orquestación exclusiva de  **efectos secundarios asíncronos**  (notificaciones push, sincronización con OTAs post-commit). Se prohíbe que n8n contenga lógica de negocio, cálculos de precios o persistencia de estado primario.

#### 9\. Seguridad, Escalabilidad y Observabilidad

##### Seguridad y Aislamiento

La autenticación se basa en  **JWT (Supabase Auth)** . El aislamiento multi-inquilino se garantiza a nivel de base de datos mediante  **Row Level Security (RLS)** , asegurando que ningún proceso pueda filtrar datos entre diferentes organizaciones.

##### Escalabilidad y Degradación Graciosa

En caso de fallo crítico de la capa de IA, ATLAS implementa una  **Degradación Graciosa** : el sistema desactiva el razonamiento de agentes y activa flujos deterministas de reserva manual para asegurar la continuidad operativa del hotel.

#### 10\. Hoja de Ruta del MVP y Evolución (Roadmap)

##### Cronograma de Fases (MVP)

* **Fase 0-2:**  Foundation, Core Platform (PMS), AI Foundation (Orquestador).  
* **Fase 3-5:**  Guest Ops (WhatsApp), Revenue Intelligence, Growth Intelligence.  
* **Fase 6-8:**  Workflow Automation, Booking Engine Directo, Operational Intel.  
* **Fase 9-10:**  Analytics Ejecutivos y Production Hardening (Seguridad y Costos).

##### Estadios de Evolución

1. **AI-Assisted:**  Humano al mando asistido por herramientas.  
2. **Intelligent Ops:**  Sugerencias proactivas del sistema.  
3. **Autonomous Optimization:**  Ejecución automática bajo políticas.  
4. **Hospitality OS:**  Interfaz primaria conversacional completa.  
5. **Hospitality Intelligence:**  Plataforma estratégica de análisis de mercado.

#### 11\. Registro de Decisiones Arquitectónicas (ADR Log)

* **ADR-002: Event-Driven Architecture.**  
* *Decisión:*  Comunicación vía eventos inmutables para garantizar desacoplamiento y permitir el  *replay*  de datos histórico.  
* **ADR-007: MCP-Ready Architecture.**  
* *Decisión:*  Abstracción de capacidades como herramientas MCP ( *Model Context Protocol* ) para garantizar interoperabilidad futura con ecosistemas de agentes externos.  
* **ADR-009: Event Sourcing Ready.**  
* *Decisión:*  Mantener compatibilidad futura para reconstrucción histórica total, aunque el estado actual sea relacional.


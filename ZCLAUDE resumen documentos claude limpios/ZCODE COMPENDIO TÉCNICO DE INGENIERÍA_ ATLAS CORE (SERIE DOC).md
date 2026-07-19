### COMPENDIO TÉCNICO DE INGENIERÍA: ATLAS CORE (SERIE DOC)

ATLAS se define como un  **AI-Native Hospitality Operating System** , un ecosistema de software diseñado para centralizar la operación compleja de alquileres temporarios. A diferencia de los sistemas tradicionales (PMS/CRM) que integran la IA como un módulo periférico, ATLAS posiciona la inteligencia agéntica como el núcleo que orquestra la lógica de negocio, la optimización de ingresos y la interacción con el huésped. Este compendio consolida las especificaciones de arquitectura de datos, infraestructura, seguridad y protocolos de validación, actuando como la  **Fuente Única de Verdad (SSOT)**  para el desarrollo del sistema.

#### 1\. PRINCIPIOS ARQUITECTÓNICOS FUNDAMENTALES

Los siguientes 20 principios gobiernan las decisiones de ingeniería y tienen prioridad sobre cualquier implementación puntual:

1. **AI-Native:**  La arquitectura se diseña alrededor de agentes. Si una tarea puede ser resuelta por un agente, la interfaz se adapta al flujo de este y no a formularios estáticos.  
2. **Memory First:**  Ninguna interacción se descarta. Todo evento alimenta la memoria persistente (Brains), asegurando que el sistema aprenda de cada decisión previa.  
3. **Context Before Prompt:**  Los LLMs nunca operan en el vacío. Cada ejecución recibe contexto curado (Guest, Property o Growth Brain) para minimizar alucinaciones.  
4. **Event Driven:**  El sistema reacciona a hechos inmutables (ej. ReservationCreated). Los agentes actúan como suscriptores de estos flujos de eventos.  
5. **Automation First:**  Los procesos repetitivos se transforman en workflows para minimizar la carga operativa manual.  
6. **Human in the Loop (BDVL):**  El sistema opera bajo un marco de validación (Business Decision Validation Layer) que define niveles de autonomía según el riesgo.  
7. **WhatsApp First:**  Interfaz primaria de operación. La web es un complemento para visualizaciones complejas y gestión administrativa.  
8. **API First:**  La lógica reside estrictamente en servicios y APIs; el frontend es una capa de presentación desacoplada.  
9. **Stateless Agents:**  Los agentes no mantienen estado interno. La persistencia es delegada a las capas de pgvector y PostgreSQL, permitiendo escalado horizontal sin sesiones persistentes en el edge.  
10. **Single Source of Truth (SSOT):**  Cada dato operativo tiene un único origen oficial dentro de PostgreSQL para garantizar consistencia.  
11. **Modularidad:**  Evolución independiente de módulos (Revenue, Messaging, Cleaning) mediante bajo acoplamiento.  
12. **Escalabilidad Horizontal:**  Arquitectura preparada para escalar de una a miles de propiedades sin rediseño estructural.  
13. **Cost Efficiency:**  Prioridad a servicios serverless y modelos de pago por uso para optimizar el margen operativo del MVP.  
14. **Security by Design:**  Autenticación y Row-Level Security (RLS) integradas desde la definición del esquema de datos.  
15. **Explainability:**  Toda recomendación de la IA debe ser rastreable y explicable, evitando el modelo de "caja negra".  
16. **Observability:**  Telemetría exhaustiva de latencias, ejecución de workflows y consumo granular de tokens.  
17. **Progressive Intelligence:**  Evolución por fases: Asiste, Sugiere, Automatiza, Optimiza y Predice.  
18. **MVP First:**  Desarrollo enfocado en capacidades que reduzcan carga operativa o aumenten ingresos de forma inmediata.  
19. **Hospitality Native:**  Decisiones técnicas alineadas con las necesidades reales de la industria (estacionalidad extrema, dobles monedas).  
20. **Evolución Compatible:**  Uso riguroso de Decisiones Arquitectónicas (ADR) para mantener compatibilidad entre versiones.

#### 2\. ARQUITECTURA DE BASE DE DATOS (DOC-0011)

##### Pila Tecnológica

* **Motor Principal:**  PostgreSQL (vía Supabase).  
* **Extensiones:**  pgvector (almacenamiento de embeddings), pgjwt (autenticación), pg\_cron.  
* **Almacenamiento:**  Supabase Storage para activos binarios.  
* **Realtime:**  Supabase Realtime para sincronización de estados en la interfaz.

##### Modelo de Multi-tenancy

El aislamiento es mandatorio y se implementa a nivel de base de datos mediante la columna organization\_id. El uso de  **UUIDs**  como llaves primarias es obligatorio para garantizar unicidad global, seguridad en APIs y facilitar futuras replicaciones de datos entre regiones.

##### Categorización de Datos

Categoría,Descripción,Ubicación  
Operacionales,"Estado determinístico y transaccional (Reservas, Unidades, Huéspedes).",PostgreSQL (Tablas relacionales)  
Configuración,"Reglas de negocio, permisos, temporadas y preferencias.",PostgreSQL  
Analíticos,"Métricas de rendimiento, ocupación y logs de ejecución.",PostgreSQL / Logs estructurados  
Conocimiento Semántico,"Memoria de Brains, embeddings y resúmenes de contexto.",PostgreSQL (pgvector)

##### Eventos de Dominio y Persistencia

ATLAS utiliza el  **Transactional Outbox Pattern**  para garantizar la consistencia entre el estado de la base de datos y los efectos secundarios asíncronos.

* **Atomicidad:**  Los hechos inmutables (ej. PaymentReceived) se registran en una tabla de outbox dentro de la misma transacción que el cambio de estado operativo.  
* **Política de Timestamps:**  Todos los registros deben utilizar  **UTC** . Se requiere el uso de created\_at, updated\_at y deleted\_at para implementar una política de  **Soft Delete**  sistemática.

#### 3\. MODELO DE INFRAESTRUCTURA Y DESPLIEGUE (DOC-0017)

##### Stack de Despliegue MVP

* **Framework & Backend:**  Next.js desplegado en Vercel.  
* **Base de Datos & Auth:**  Supabase (PostgreSQL, Edge Functions).  
* **IA:**  Orchestrator agnóstico (Claude/OpenAI) con routing por nivel de ejecución.  
* **Nota de Divergencia:**  Aunque n8n es la elección estratégica para orquestación compleja, en la Fase 1 se opera mediante  **Vercel Serverless Functions**  para minimizar el "premature overengineering".

##### Estrategia de Escalabilidad

Se adopta un enfoque de  **monolito modular** . Los componentes están separados lógicamente para permitir que, ante cuellos de botella medibles, se extraigan hacia microservicios o workers independientes sin afectar el núcleo del sistema.

##### Observabilidad y Telemetría

* **Latencia de API:**  Monitoreo de tiempos de respuesta en cada endpoint de servicio.  
* **Ejecución de Agentes:**  Trazabilidad de éxitos y fallos en orquestaciones agénticas.  
* **Costo de IA:**  Registro obligatorio del consumo de tokens vinculado a la organization\_id para análisis de rentabilidad.

##### Estrategia de Backup y DR

* **RPO (Recovery Point Objective):**  ≤ 5 minutos.  
* **RTO (Recovery Time Objective):**  ≤ 30 minutos.  
* **Niveles:**  Daily Full Backups, Point-in-Time Recovery (PITR) y validación periódica de restauración.

#### 4\. SEGURIDAD Y MANUAL OPERATIVO DE MULTI-TENANCY (DOC-0031)

##### Implementación de Row-Level Security (RLS)

El aislamiento no es una responsabilidad de la aplicación, sino una restricción forzosa de la base de datos. Ninguna consulta puede acceder a datos ajenos gracias a las políticas de RLS vinculadas al organization\_id. Para la autenticación, se utiliza un  **Custom Access Token Hook**  en Supabase para inyectar y reclamar el organization\_id de forma segura.

##### Checklist Obligatorio de RLS (Pre-merge)

*  ¿La tabla tiene la columna organization\_id o property\_id?  
*  ¿RLS está activado formalmente (ENABLE ROW LEVEL SECURITY)?  
*  ¿Existe una política de SELECT filtrando por el tenant autenticado?  
*  ¿Existen políticas equivalentes para INSERT, UPDATE y DELETE?  
*  ¿Se ha verificado el aislamiento con dos organizaciones de prueba simultáneas?  
*  ¿Los procesos de background (workers) incluyen filtros explícitos en sus queries?  
*  ¿Está documentada la excepción si la tabla es intencionalmente global?

##### Arquitectura de API Keys

Nivel,Ubicación,Alcance  
Sistema,Variables de entorno (Vercel),"Infraestructura base, proveedores de IA y servicios core."  
Propietario,Tabla property\_integrations (RLS activo),"Claves de canales (OTA, WhatsApp) específicas de cada tenant."

##### Guardrails de IA

Se previene la fuga de datos asegurando que la recuperación de contexto para los  *Brains*  se realice mediante queries pre-filtradas por RLS. Los agentes nunca reciben información que no pertenezca al tenant originario de la consulta.

#### 5\. ESPECIFICACIÓN DEL MODELO DE DATOS \- FASE 1 (DOC-0032)

##### Entidades Core (13 Tablas Desplegadas)

1. organizations: Entidad raíz del multi-tenancy.  
2. organization\_members: Vínculo usuarios-organización (Auth Hook).  
3. properties: Activos inmobiliarios.  
4. units: Unidades reservables dentro de una propiedad.  
5. guests: Perfiles de huéspedes y Guest Brain persistente.  
6. reservations: Estadías, bloqueos y booking dates.  
7. pricing\_history: Histórico de precios y decisiones previas (AI/Manual).  
8. competitor\_scraper\_results: Datos de mercado para análisis comparativo.  
9. bdvl\_decisions: Registro de validaciones y aprobaciones de agentes.  
10. property\_integrations: Credenciales de terceros cifradas (AES-256).  
11. execution\_logs: Telemetría de llamadas IA y costos asociados.  
12. content\_assets: Material para marketing (Growth Brain).  
13. content\_performance: Métricas de conversión de contenidos.

##### Atributos Críticos de Tabla

* **competitor\_scraper\_results** : Incluye applies\_to\_unit\_id (UUID) para mapeo 1:1 de comparabilidad, adjustment\_pct (numeric) para ponderar diferencias de calidad, y source\_url.  
* **pricing\_history** : Incluye adjustment\_note (text) para justificar cambios automáticos y organization\_id obligatorio.  
* **Regla de Integridad** : El campo organization\_id es mandatorio incluso en entornos mono-tenant iniciales para facilitar la transición a la Fase 2\.

#### 6\. ESTRATEGIA DE PRUEBAS TÉCNICAS (DOC-0033)

##### Matriz de Pruebas Obligatorias

\#,Test,Qué Cubre  
1,Aislamiento de lectura,Verifica que Org A no pueda leer datos de Org B vía RLS.  
2,Aislamiento de escritura,Impide que una Org modifique registros de otra.  
3,BDVL Critical (L3/L4),Valida que acciones de alto riesgo requieran approved\_by.  
4,BDVL Low Risk (L0),Verifica ejecución sin bloqueo para tareas determinísticas.  
5,Latencia L1-L2,Respuesta del sistema en \< 2 segundos para interacción fluida.  
6,Registro de Costos,Persistencia obligatoria de cost\_usd en logs por llamada IA.  
7,Graceful Degradation,Manejo de fallos en LLM (fallback) sin caída del servicio.  
8,Piso de Contexto,Verificación de inyección de datos de propiedad en prompts.  
9,Scraper Integrity,El worker debe filtrar por property\_id antes de persistir.  
10,Fast Path L0,Consultas de FAQ/WiFi deben responder sin invocar LLM.

##### Protocolo BDVL (Business Decision Validation Layer)

Clasificación de acciones según ARCH-0022:

* **L0 (Deterministic):**  Consultas directas (ej. WiFi, horarios). Ejecución vía SQL, no invoca LLM.  
* **L1 (Assistance):**  IA recomienda, humano ejecuta.  
* **L2 (Proposal):**  IA propone acción, humano aprueba.  
* **L3 (High Risk):**  Cambios de precio en alta temporada o cancelaciones. Requiere firma approved\_by.  
* **L4 (Autonomous):**  Ejecución automática bajo políticas estrictas (Diferido para Fase 2+).

#### 7\. CONOCIMIENTO DE DOMINIO Y LÓGICA DE NEGOCIO (ANEXO)

##### Doble Moneda y Computación

ATLAS utiliza  **USD como moneda canónica de cómputo**  para proteger la lógica de ingresos de la volatilidad inflacionaria. El  **ARS se utiliza exclusivamente para visualización**  (display), realizando conversiones basadas en tipos de cambio actualizados al momento de la consulta.

##### Modelos de Venta (Revenue Logic)

1. **Modelo Noche (per\_night):**  Aplicado en temporada baja con tarifas diferenciadas entre semana (L-J) y fines de semana (V-D).  
2. **Modelo Paquete (package):**  Aplicado en alta temporada (ej. enero, Semana Santa). El precio se define por la estadía completa (mínimo 7 noches), ignorando el prorrateo individual por noche.

##### Conceptos de Hospitalidad Agéntica

* **Lead Time:**  Días entre la reserva y el check-in. Define la agresividad del pricing.  
* **Booking Pace:**  Ritmo de ventas actual comparado con el histórico. Si el pace es \< 0.85 respecto al año anterior, el agente debe sugerir ajustes.  
* **Techo de Precio (Pricing Ceiling):**  Límite superior definido por el costo de destinos alternativos (ej. Brasil). Ninguna recomendación de la IA debe superar este techo para mantener la competitividad regional.


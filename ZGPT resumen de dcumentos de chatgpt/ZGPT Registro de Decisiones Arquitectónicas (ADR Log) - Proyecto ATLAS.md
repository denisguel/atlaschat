### Registro de Decisiones Arquitectónicas (ADR Log) \- Proyecto ATLAS

#### 1\. Información del Documento y Control de Versiones

##### Metadatos del Proyecto

Campo,Valor  
Proyecto,ATLAS (AI-Native Hospitality Operating System)  
Versión Global,2.0  
Estado,Canónico  
Fecha de Actualización,2026-06-29

##### Historial de Revisiones

Versión,Fecha,Descripción del Cambio,Estado  
1.0,2026-01-15,Definición inicial de componentes y flujo de datos.,Superado  
2.0,2026-06-29,"Consolidación de arquitectura AI-Native, Niveles de Ejecución, BDVL y modelo de consistencia final.",Aceptado (Actual)

#### 2\. Introducción y Propósito del Registro

ATLAS se define como un sistema  **AI-Native** , donde la inteligencia artificial no es un componente periférico, sino el núcleo de la orquestación operativa. A diferencia de sistemas tradicionales, en ATLAS el usuario interactúa con la IA y esta opera el software de forma determinista.Este registro es el documento canónico de autoridad técnica. Su cumplimiento es obligatorio para evitar la  **deriva arquitectónica**  y garantizar que la evolución del sistema mantenga la separación estricta entre el razonamiento probabilístico (IA) y la ejecución determinista (Servicios de Dominio).

##### Principios Fundamentales de Diseño (ARCH-014 / ARCH-024)

* **PostgreSQL como SSOT:**  PostgreSQL es la única fuente de verdad (Single Source of Truth) para el estado operativo y transaccional.  
* **Separación de Memoria y Estado:**  Los "Brains" almacenan conocimiento semántico y experiencia, nunca estados de negocio transaccionales.  
* **Atribución de Contexto:**  Todo dato recuperado por la IA para generar una respuesta debe ser trazable a su origen exacto.  
* **Recuperación Determinista:**  La recuperación de memoria es un proceso de software previo al razonamiento; la IA solo razona  *después*  de que el contexto ha sido inyectado.  
* **Optimización por Relevancia:**  La memoria del sistema se optimiza para la relevancia contextual, no para la completitud de datos históricos.

#### 3\. Índice Consolidado de Decisiones Arquitectónicas

ID,Título de la Decisión,Estado,Prioridad/Clasificación  
ADR-002,Arquitectura Orientada a Eventos (EDA),Aceptado,Crítica  
ADR-003,WhatsApp como Interfaz Primaria,Aceptado,Estratégica  
ADR-005,"Arquitectura de Memoria basada en ""Brains""",Aceptado,Crítica  
ADR-006,n8n como Motor de Orquestación de Workflows,Aceptado,Estratégica  
ADR-007,"Diseño ""MCP-Ready""",Aceptado,Estratégica  
ADR-009,Preparación para Event Sourcing,Aceptado,Evolutiva  
ARCH-021,Niveles de Ejecución AI (Pirámide),Aceptado,Crítica  
ARCH-022,Business Decision Validation Layer (BDVL),Aceptado,Crítica  
ARCH-023,Estrategia de UX Operativa (Matriz de Interacción),Aceptado,Estratégica  
ARCH-024,Modelo de Consistencia de Estado (SSOT),Aceptado,Crítica  
ARCH-025,Arquitectura Física del MVP (Modular Monolith),Aceptado,Crítica

#### 4\. Cuerpo del Registro: Estrategia y Comunicación

##### ADR-002: Arquitectura Orientada a Eventos (EDA)

**Decisión:**  ATLAS adopta EDA como modelo primario de comunicación asíncrona para desacoplar agentes de IA y servicios de dominio.  **Reglas Críticas para Suscriptores:**

*  Deben ser idempotentes (tolerar entregas duplicadas).  
*   **PROHIBIDO:**  Modificar el evento recibido en el bus.  
*   **PROHIBIDO:**  Asumir un orden de ejecución entre diferentes agregados.  
*   **PROHIBIDO:**  Crear cadenas de eventos circulares (Event Loops).

##### ADR-003 / ARCH-023: WhatsApp e Interfaz Híbrida

**Justificación:**  El mercado LATAM opera sobre movilidad y baja fricción. El uso de WhatsApp elimina la curva de aprendizaje del PMS tradicional.**Matriz de Interacción (UX Strategy):**  | Tarea / Contexto | Interfaz Recomendada | Responsabilidad | | :--- | :--- | :--- | | Operación Diaria (Check-in, FAQs) |  **WhatsApp**  | Interacción rápida y comandos IA. | | Gestión de Reservas Individuales |  **WhatsApp**  | Flujo conversacional. | | Análisis de Calendario y Ocupación |  **Dashboard Web**  | Visualización densa y espacial. | | Configuración de Precios Masivos |  **Dashboard Web**  | Edición en bloque y comparativas. | | Administración y Permisos |  **Dashboard Web**  | Gobernanza y seguridad. |

#### 5\. Cuerpo del Registro: Inteligencia y Memoria

##### ADR-005: Arquitectura de Memoria basada en "Brains"

**Decisión:**  Los LLM son  *stateless* . La persistencia semántica se segmenta en tres dominios.

* **Guest Brain:**  Preferencias y estilo.  **No**  almacena IDs de reserva ni saldos.  
* **Property Brain:**  Reglas de la casa y manuales.  **No**  almacena inventario físico real.  
* **Growth Brain:**  Estrategias y rendimiento.  **No**  almacena transacciones financieras.  **Regla Arquitectónica:**  Los agentes consumen memoria a través del  *Context Builder*  pero no poseen ni gestionan el ciclo de vida de los Brains.

##### ADR-006: n8n como Orquestador de Efectos Secundarios

**Decisión:**  n8n coordina integraciones externas y tareas programadas.  **Restricciones de Propiedad:**  n8n  **NO**  tiene permitido poseer lógica de cálculo de precios, reglas de autorización de usuarios ni persistencia oficial del estado de negocio. Su rol es estrictamente de coordinación de efectos secundarios.

##### ADR-007: Diseño MCP-Ready

**Filosofía:**  ATLAS se diseña bajo el estándar de  *Model Context Protocol* .**Directriz Técnica:**  "Las capacidades de negocio se exponen como  **Herramientas (Tools)** , no como Prompts". La IA razona sobre la intención, pero la ejecución es delegada a herramientas deterministas con contratos de entrada/salida estrictos.

#### 6\. Cuerpo del Registro: Evolución y Persistencia

##### ARCH-024: Modelo de Consistencia de Estado

* **PostgreSQL es la SSOT única.**  
* **Patrón "Commit Before Publish":**  Es obligatorio confirmar la transacción en la DB antes de publicar el evento en el bus para evitar inconsistencias distribuidas.  
* **Consistencia:**  Fuerte para operaciones transaccionales; Eventual para proyecciones de lectura (Analytics/Dashboards).

##### ADR-009: Preparación para Event Sourcing

Aunque el estado actual es relacional, cada transición debe emitir un evento de dominio. Esto permite que, en fases futuras, el estado de un agregado (ej. una reserva) pueda ser reconstruido íntegramente desde su log de eventos (Aggregate Stream).

#### 7\. Cuerpo del Registro: Capas de Ejecución AI y Seguridad

##### ARCH-021: Pirámide de Ejecución AI

Para optimizar latencia y costo, se aplica el nivel mínimo de inteligencia necesario:  
       / \\  
      / L4 \\  \--\> Estrategia: Razonamiento profundo (Multi-agente \+ Planner)  
     /------\\  
    /   L3   \\ \--\> Complejo: Coordinación multi-dominio (Revenue \+ Ops)  
   /----------\\  
  /     L2     \\ \--\> Contextual: Agente único con recuperación de Brains  
 /--------------\\  
/      L1/L0     \\ \--\> Trivial: Normalización, reglas SQL o prompts básicos  
\------------------

##### ARCH-022: Business Decision Validation Layer (BDVL)

Capa obligatoria entre el razonamiento de la IA y la ejecución de servicios. | Nivel de Riesgo | Acción Requerida | Ejemplo | | :--- | :--- | :--- | |  **Bajo**  | Ejecución Automática | Responder FAQ de WiFi. | |  **Medio**  | Validación de Políticas | Crear tarea de limpieza. | |  **Alto**  | Confirmación de Usuario | Modificar fechas de reserva. | |  **Crítico**  |  **Aprobación Humana Obligatoria**  | Reembolsos, cancelaciones, borrado de datos. |**Nuance Técnica:**  El riesgo de negocio siempre invalida el "Confidence Score" de la IA. Si una acción es Crítica, se requiere humano aunque la IA tenga 99% de confianza.

#### 8\. Cuerpo del Registro: Arquitectura Física y Despliegue

##### ARCH-025: Modular Monolith (MVP)

Se despliega como un monolito modular para maximizar la eficiencia de costos y reducir la latencia de red. La separación lógica es estricta para permitir una extracción futura a microservicios solo bajo evidencia de cuellos de botella.

##### ARCH-027: Optimización de Costos AI

1. **Model Routing:**  Selección dinámica del modelo (ej. GPT-4o para L3, Haiku para L1).  
2. **Prompt Compression:**  Resumen de historial antes de inyección de contexto.  
3. **Context Budget:**  Límites estrictos de tokens recuperados por el Context Builder.

#### 9\. Matriz de Interdependencias y Trazabilidad

Componente,Decisión Relacionada,Ref. Técnica  
Revenue Brain,ADR-005 (Brains),RFC-0008  
WhatsApp Adapter,ADR-003 / ARCH-023,ARCH-0023  
Event Store (PG),ADR-002 / ADR-009,ARCH-0024  
Tool Dispatcher,ADR-007 (MCP),RFC-0010

##### Prohibiciones Arquitectónicas (Architectural Prohibitions)

* 🚫  **IA escribiendo directamente en la DB:**  Riesgo de corrupción de estado no determinista. Debe pasar por un Servicio de Dominio.  
* 🚫  **Lógica de negocio en el Frontend:**  Impide la omnicanalidad (WhatsApp/Web) y centralización de reglas.  
* 🚫  **Comunicación directa entre Agentes:**  Causa bucles infinitos y pérdida de control. Toda coordinación pasa por el  *AI Orchestrator* .

#### 10\. Glosario de Terminología Arquitectónica

* **SSOT (Single Source of Truth):**  PostgreSQL como origen único de la verdad operativa.  
* **BDVL (Business Decision Validation Layer):**  Filtro de seguridad que valida propuestas de IA contra reglas de negocio deterministas.  
* **MCP (Model Context Protocol):**  Protocolo para estandarizar la exposición de capacidades del sistema hacia la IA.  
* **Brain:**  Almacén de conocimiento semántico (Guest, Property, Revenue) que informa a la IA sin poseer estado operativo.  
* **Aggregate Stream:**  Secuencia de eventos de una entidad única; el orden solo se garantiza dentro de este flujo específico.  
* **Context Builder:**  Motor que filtra y comprime datos de los Brains para crear el prompt final.  
* **Commit Before Publish:**  Garantía de que un evento solo llega al bus si su cambio de estado ya es persistente en la SSOT.


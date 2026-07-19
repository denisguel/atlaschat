### COMPENDIO FOUNDATION: ATLAS — AI-Native Hospitality Operating System

#### 1\. ADN DEL PROYECTO Y VISIÓN ESTRATÉGICA

ATLAS representa el primer sistema operativo de hospitalidad diseñado bajo una filosofía  **AI-Native** , donde la inteligencia artificial no es una funcionalidad agregada, sino el núcleo que orquesta la ejecución del software.

##### Misión y Visión

* **Misión:**  Maximizar la rentabilidad de alojamientos independientes mediante un sistema operativo impulsado por IA que reduzca el trabajo operativo y mejore continuamente las decisiones comerciales.  
* **Visión:**  Convertirse en el estándar tecnológico para la hospitalidad independiente en Latinoamérica, democratizando la operación profesional de la misma forma que Shopify democratizó el e-commerce.

##### El Problema y el Target User

* **Target User:**  Operadores independientes de  **1 a 50 unidades**  en Latinoamérica (enfocado inicialmente en destinos de estacionalidad extrema como la Costa Atlántica Argentina).  
* **El Problema:**  La industria STR (Short-Term Rental) sufre de una fragmentación operativa crítica y una dependencia tóxica de las OTAs (Booking, Airbnb). Los propietarios operan de forma reactiva con datos aislados en múltiples herramientas (WhatsApp, Excel, Calendar, OTAs), resultando en pérdida de reservas, errores de pricing y un techo insalvable en su capacidad de escalabilidad.

##### Métricas de Éxito (North Star & KPI)

* **North Star Metric:**   **Incremental Gross Profit per Property (IGPPP)** . El éxito se mide por el aumento neto en la utilidad del propietario.  
* **Métricas Secundarias:**  
* Intervenciones humanas evitadas (automatización real).  
* Tasa de repetición de huéspedes.  
* Horas operativas ahorradas por propiedad/mes.  
* Conversión de consultas WhatsApp-a-Reserva.  
* RevPAN (Revenue per Available Night).

##### Qué NO es ATLAS

* No es un PMS tradicional (centrado en carga manual y dashboards).  
* No es un simple chatbot de FAQ (es un agente de decisión y ejecución).  
* No es una agencia de gestión de propiedades (es la tecnología que las potencia).  
* No depende de un único proveedor de IA (arquitectura agnóstica a modelos).

#### 2\. PRINCIPIOS DE PRODUCTO (LA CONSTITUCIÓN DE ATLAS)

Estos 20 principios gobiernan toda decisión arquitectónica. Si una funcionalidad contradice un principio, DEBE ser rediseñada.

1. **Principio 1 — AI Native:**  La IA es el núcleo. Si un agente puede resolver la tarea, la arquitectura se diseña alrededor del agente, no de un formulario.  
2. **Principio 2 — Memory First:**  Toda interacción genera conocimiento. NUNCA se descarta información útil: conversaciones, preferencias de huéspedes, horarios de check-in, incidencias, cancelaciones, comportamientos y decisiones tomadas (aceptadas o rechazadas).  
3. **Principio 3 — Context Before Prompt:**  Los LLM nunca responden solo con el prompt; siempre reciben contexto de los "Brains" (Guest, Property, Growth), historial de reservas, precios y eventos del sistema.  
4. **Principio 4 — Event Driven:**  Todo cambio (ReservaCreada, ReservaCancelada, CheckOut) genera un evento al que los agentes reaccionan de forma asíncrona.  
5. **Principio 5 — Automation First:**  Toda tarea repetitiva (instrucciones, recordatorios de pago, reportes) DEBE convertirse en un workflow automatizable.  
6. **Principio 6 — Human in the Loop:**  La automatización tiene tres niveles:  **Nivel 1 (IA recomienda, humano ejecuta)** ;  **Nivel 2 (IA propone, humano aprueba)** ;  **Nivel 3 (IA ejecuta automáticamente)** .  
7. **Principio 7 — WhatsApp First:**  WhatsApp es la interfaz primaria de operación. La web es complementaria. Operar el negocio no debe requerir una PC.  
8. **Principio 8 — API First:**  Toda funcionalidad reside en APIs internas. La interfaz nunca contiene lógica crítica de negocio.  
9. **Principio 9 — Stateless Agents:**  Los agentes no guardan memoria propia; esta reside en PostgreSQL o Vector Stores para ser resiliente y escalable.  
10. **Principio 10 — Single Source of Truth:**  Cada dato tiene un único origen oficial (ej. Disponibilidad → Calendario interno; Perfil → Guest Brain).  
11. **Principio 11 — Modularidad:**  Módulos como Revenue, Messaging o Payments deben evolucionar de forma independiente y bajo acoplamiento.  
12. **Principio 12 — Escalabilidad Horizontal:**  El sistema DEBE soportar desde una unidad hasta miles sin rediseño arquitectónico.  
13. **Principio 13 — Cost Efficiency:**  El desarrollo prioriza servicios serverless y pago por uso. Se optimiza el uso de modelos (L1-L4) para minimizar costos operativos.  
14. **Principio 14 — Security by Design:**  La seguridad (autenticación, cifrado AES-256, RLS) es un requisito de diseño inicial, no una etapa posterior.  
15. **Principio 15 — Explainability:**  Toda decisión de la IA (ej. aumento de precio) DEBE ser explicable para el propietario; no existen cajas negras.  
16. **Principio 16 — Observability:**  Todo proceso genera logs, métricas y trazas auditables en tiempo real.  
17. **Principio 17 — Progressive Intelligence:**  El sistema aprende: primero asiste, luego sugiere y finalmente automatiza y predice.  
18. **Principio 18 — MVP First:**  Solo se desarrollan funciones que aumenten ingresos, reduzcan trabajo o mejoren la experiencia del huésped.  
19. **Principio 19 — Hospitality Native:**  Las funciones deben resolver necesidades reales de hospitalidad independiente, no ser generalistas.  
20. **Principio 20 — Evolución Compatible:**  Nuevas versiones DEBEN mantener compatibilidad con modelos de datos previos o documentar rupturas vía ADR.

##### Checklist para nuevas funcionalidades (Gobernanza)

Antes de aprobar una funcionalidad, verificar:

1. ¿Respeta AI Native?  
2. ¿Genera eventos?  
3. ¿Alimenta la memoria?  
4. ¿Puede automatizarse?  
5. ¿Es escalable?  
6. ¿Tiene API?  
7. ¿Es segura y auditable?  
8. ¿Reduce trabajo del propietario?  
9. ¿Aporta valor real al MVP?  
10. ¿Es explicable?

#### 3\. LA TESIS DEL MOAT AGÉNTICO Y ESTRATEGIA DE MERCADO

ATLAS se posiciona en la capa de distribución vía agentes (ChatGPT, Claude, Perplexity) mediante el protocolo  **MCP (Model Context Protocol)**  y el  **UCP (Universal Commerce Protocol)** .

##### Las 4 Capas del Moat

1. **Discoverability Agéntica:**  Cada propiedad se expone como un servidor MCP/UCP. Permite el  **Merchant-controlled checkout** : la IA maneja el descubrimiento; el pago se cierra vía WhatsApp/MercadoPago, reteniendo ATLAS el control de la reserva frente a la comisión de las OTAs.  
2. **Content Performance Loop:**  Datos acumulados sobre qué hooks y formatos generan reservas reales, optimizados por IA en un ciclo de aprendizaje continuo.  
3. **Decision Graph:**  El historial de decisiones del propietario (aprobaciones/rechazos de precios) genera un entrenamiento específico irreplicable por otras IAs.  
4. **Densidad Regional:**  Dataset local único (ej. Costa Atlántica) que permite un efecto de red de pricing cooperativo y contenido contextualizado.

##### Análisis de Competidores

Actor,Limitación Estratégica,Ventaja de ATLAS  
Booking/Airbnb,Modelo basado en comisión. El tráfico agéntico directo canibaliza su propio revenue.,Fomenta reservas directas (0% comisión OTAs) mediante protocolos agénticos.  
PMS Tradicionales,Arquitectura  Dashboard-first . Requieren rearquitectura total para ser inteligentes.,Arquitectura  AI-native  desde el origen. Operación vía lenguaje natural.  
CRS Enterprise,"Diseñados para \+35,000 llaves. Inaccesibles por costo y complejidad para el pequeño dueño.",Democratiza tecnología Enterprise para dueños de 1-50 unidades con modelo serverless.

#### 4\. CONOCIMIENTO DE DOMINIO: EL NEGOCIO DE STR EN LATAM

##### Particularidades del Mercado

* **Doble Moneda:**  El costo operativo es en Pesos (ARS), pero la moneda canónica de cómputo es el Dólar (USD) para proteger el margen contra la inflación.  
* **Techo de Precio:**  El límite máximo no lo define la competencia local, sino el  **Costo de un destino alternativo comparable (ej. Brasil)** . Superar este techo vacía la unidad.  
* **Modelos de Venta:**  
* *Temporada Alta:*  Modelo  **Paquete**  (7 noches, Sábado a Sábado).  
* *Temporada Baja:*  Modelo  **Por Noche**  (Tarifas diferenciadas L-J vs V-D).

##### Lógica Operativa de Revenue Management

###### *Umbrales de Gap Nights (Noches Huérfanas)*

El sistema DEBE identificar y actuar sobre vacancias según la temporada:| Temporada | Definición de Gap | Acción Prioritaria || \------ | \------ | \------ || **Baja/Media** | 1 noche suelta | Ofrecer extensión a huéspedes adyacentes (Costo Adquisición 0). || **Fines de Semana Largos** | 1 noche huérfana en paquete de 3 | Precio agresivo o extensión obligatoria. || **Vacaciones Invierno** | 2 noches sueltas (no llegan al paquete de 4\) | Precio agresivo o flexibilizar mínimo. || **Alta Temporada** | 3-4 noches sueltas (no completan la semana) | Flexibilizar mínimo solo si la fecha es próxima; si no, esperar paquete de 7\. |

###### *Umbrales de Booking Pace (Ritmo de Reservas)*

Se calcula como el ratio de reservas actuales vs. histórico del mismo ciclo:| Ratio | Diagnóstico | Acción || \------ | \------ | \------ || **\> 1.15** | Adelantado | Oportunidad de SUBIR precio. || **0.85 \- 1.15** | En línea | Mantener/Ajuste fino. || **0.60 \- 0.85** | Atrasado | Revisar competencia y bajar precio. || **\< 0.60** | **Alerta Crítica** | Acción correctiva urgente; promoción agresiva. |

#### 5\. ARQUITECTURA DEL SISTEMA Y COMPONENTES CORE (RFC)

ATLAS invierte el flujo tradicional:  **Usuario \-\> IA \-\> Software (Execution Layer)** .

##### AI Orchestrator (RFC-0002)

Coordina la ejecución sin poseer lógica de negocio. Define niveles de ejecución para optimizar costos y precisión:

* **L0 (Determinístico):**  Consultas directas a DB (ej. "¿Cuál es el WiFi?").  
* **L1 (Intención):**  Clasificación de solicitud.  
* **L2-L4 (Razonamiento):**  Uso de Claude para planes complejos y recomendaciones de revenue.  
* **SSOT:**  PostgreSQL es la única Fuente de Verdad. La IA nunca es el SSOT.

##### Memory System (Brains) (RFC-0003)

* **Guest Brain:**  Historial, preferencias, score de comportamiento y sentiment analysis.  
* **Property Brain:**  Manuales, amenities, reglas y mantenimiento (Conocimiento Estructurado).  
* **Growth Brain:**  Performance de contenido, canales de adquisición y atribución.

##### Multi-Agent System (RFC-0004)

Agentes especializados (Revenue, Guest Relations, Content, Operations) que razonan sobre dominios específicos coordinados por el Orchestrator.

##### Event-Driven Architecture (RFC-0005)

Utiliza el patrón  **Transactional Outbox**  en PostgreSQL. Los eventos se publican en el bus solo después de que la transacción de negocio ha sido confirmada en la DB.

#### 6\. ESPECIFICACIÓN DEL MVP Y MODELO DE DATOS

##### Stack Tecnológico

* **Frontend/Backend:**  Next.js, React, TypeScript.  
* **Base de Datos/Auth:**  Supabase (PostgreSQL).  
* **Orquestación Asíncrona:**  n8n (Side-effects).  
* **IA:**   **Claude (Anthropic)**  como proveedor primario para razonamiento L2-L4.

##### Modelo de Datos (13 Tablas Deplegadas)

El sistema utiliza UUIDs universales. Las tablas core desplegadas son:

1. organizations  
2. organization\_members  
3. properties  
4. units  
5. guests  
6. reservations  
7. pricing\_history  
8. competitor\_scraper\_results  
9. bdvl\_decisions (Validación de decisiones)  
10. content\_assets  
11. content\_performance  
12. property\_integrations (Credenciales cifradas)  
13. execution\_logs (Consumo de tokens y costos)

#### 7\. GOBERNANZA: SEGURIDAD Y MULTI-TENANCY

##### Aislamiento de Tenants

Implementación obligatoria de  **Row-Level Security (RLS)** . Ninguna tabla queda exenta de la columna organization\_id o property\_id.

##### Checklist de RLS (Obligatorio Pre-Merge)

1. ¿La tabla tiene columna organization\_id?  
2. ¿RLS está ENABLED en la tabla?  
3. ¿Existe policy de SELECT que filtre por el tenant del usuario autenticado?  
4. ¿Existe policy de INSERT/UPDATE/DELETE equivalente?  
5. ¿Se testeó con dos tenants simultáneos para verificar que no hay fuga de datos?  
6. En procesos de background: ¿El filtro está codificado explícitamente en la query?

##### Niveles de API Keys

* **Nivel Sistema:**  Claves de proveedores (Claude) en variables de entorno (Vercel).  
* **Nivel Propietario:**  Claves de integraciones (PMS, OTAs) cifradas con AES-256 en la base de datos bajo RLS.

#### 8\. ESTADO DEL PROYECTO Y ROADMAP

##### Progreso Actual

* **Fase 0 (Fundación):**  HECHO. Reconciliación documental, 14 migraciones con RLS aplicado, Custom Access Token Hook configurado.  
* **Fase 1 (Revenue Agent):**  EN CURSO. Capa de mensajería abstracta construida, adaptador de Telegram funcional, ingesta de histórico real completada.

##### Gaps Críticos

1. **Entidad Message:**  Falta persistencia de mensajes; el procesamiento es transitorio (Diferido a Fase 2).  
2. **Divergencia n8n:**  Fase 1 usa Next.js Serverless para orquestación directa; n8n se reevalúa en Fase 2\.  
3. **Datos de Reservas:**  Tabla reservations vacía; impide cálculos reales de Pace y Gap Nights hasta ingesta completa.

##### Roadmap Curado

* **Fase 1 (Founding Partners):**  5-10 clientes iniciales, integración MercadoPago (cuotas), agente FAQ y Inbox unificado.  
* **Fase 2 (Intelligence Layer):**  
* **Context Builder:**  Recuperación semántica real.  
* **Scraper por fecha:**  Implementación de scraper de competencia específico para fechas consultadas (Booking \+ lindabay.com.ar).  
* **MCP Servers:**  Exposición de propiedades para descubribilidad agéntica.  
* **Fase 3+ (NeuralCore):**  Visión completa multi-agente y marketplace de servicios automatizados.


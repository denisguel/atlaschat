COMPENDIO DE AUDITORÍA, CONTROL Y ESTADO DEL PROYECTO ATLAS
Este documento consolida la "fuente de verdad" sobre la integridad del repositorio, las brechas identificadas entre la visión y la implementación, y el estado detallado de cada fase.
1. Reconciliación y Auditoría de Documentación
Se realizó una auditoría forense sobre el repositorio original (69 archivos) para establecer un corpus canónico de 53 documentos
.
Normalización: Se corrigieron nombres de archivos que rompían scripts de CI/CD y se eliminaron archivos vacíos o redundantes
.
Corrección de IDs: Se resolvieron colisiones críticas en la numeración, como en los documentos de arquitectura de memoria y sistemas multi-agente (ARCH-0013 y ARCH-0014)
.
Documentos Generados para Cerrar Gaps: Durante este proceso se crearon documentos que faltaban en el diseño original, como ARCH-0028 (Seguridad de IA y Guardrails) y ADR-010 (Reducción de alcance a Fase 1)
.
2. Análisis de Brechas (Gap Analysis)
Se identifican divergencias importantes entre la visión estratégica y lo que realmente está desplegado en la infraestructura
.
Hallazgos Críticos y Divergencias
Entidad Message sin Persistencia: Aunque es una entidad core definida en el modelo de dominio, actualmente no existe una tabla en la base de datos para los mensajes. Se procesan de forma transitoria y se descartan, lo que impide (por ahora) crear una memoria de conversación histórica
.
Divergencia en Orquestación (n8n): El ADR-006 establecía n8n como el motor de flujos, pero la Fase 1 se implementó utilizando código directo en Next.js y webhooks de Vercel. Falta un ADR formal que registre este cambio de estrategia
.
Entidades Diferidas: Las entidades de Payment, Workflow y Event no tienen tablas en la Fase 1 y su implementación se ha movido a fases posteriores sin un registro explícito de este diferimiento
.
3. Estado Actual del Proyecto (Tablero de Verdad)
El proyecto se divide operativamente en tres fases de ejecución
.
Fase 0 (Fundación): Completada. Incluye la reconciliación documental, especificación del modelo de datos, infraestructura de seguridad (RLS en Supabase) y configuración de entornos
.
Fase 1 (Revenue Agent): En curso/Núcleo construido. Ya funcionan los comandos de Telegram, la ingesta histórica real (con dólar blue), y el Revenue Agent con contexto mínimo y guardrails de decisión (BDVL)
.
Fase 2 (Intelligence Layer): En curso. Se han desplegado las tablas para el modelo de tarifas y el motor de resolución de precios. La inteligencia de decisión (subir/bajar precios basado en competencia y ocupación) está en fase de prueba
.
4. Especificación del Modelo de Pricing (Fase 2)
Este componente es vital porque el modelo original solo contemplaba un "precio único", lo cual era insuficiente para la estacionalidad de la Costa Atlántica
.
Modelos Coexistentes: El sistema ahora soporta el Modelo Noche (precio diferenciado L-J y V-D) y el Modelo Paquete (precio por estadía completa, común en alta temporada y vacaciones de invierno)
.
Techo de Precio: Se ha formalizado el concepto de "techo invisible" (ej. costo de viajar a Brasil). El agente nunca recomendará precios por encima de este techo configurable por el propietario
.
5. Gaps de Datos y Backlog de Ingeniería
Para desbloquear la Fase 2, se requiere resolver la falta de datos reales:
Reservas Vacías: La lógica de booking pace y gap nights está construida pero no tiene datos para procesar
.
Elasticidad: Sin un histórico de cómo afectaron los cambios de precio a la ocupación en el pasado, el simulador de ingresos opera bajo supuestos y no bajo inferencias reales
.
Documentación Pendiente: Existe un backlog de aproximadamente 27 documentos de ingeniería (desde DOC-0034 hasta DOC-0060) que incluyen estándares de código, glosarios y manuales de onboarding
.
Con este documento, junto a los cinco compendios anteriores que generamos, tienes el 100% de la base de conocimiento de ATLAS organizada y lista para ser utilizada de forma consolidada.
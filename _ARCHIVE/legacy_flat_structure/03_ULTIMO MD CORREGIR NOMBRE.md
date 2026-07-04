# ATLAS — Arquitectura Técnica y Runtime
## Documento 03 (Versión 2.0)
### AI-Native Hospitality Operating System

---

# PROPÓSITO

Este documento define la arquitectura técnica completa de ATLAS.

Su objetivo es construir un sistema:

- extremadamente rápido
- económicamente rentable
- fácil de mantener
- escalable
- resiliente
- preparado para IA actual y futura

Toda decisión arquitectónica responde a cinco principios:

- Latencia mínima
- Coste mínimo
- Máxima confiabilidad
- Evolución sin reescritura
- IA como complemento de un sistema determinístico

---

# PRINCIPIOS FUNDAMENTALES

ATLAS NO es un conjunto de agentes.

ATLAS es un sistema operativo compuesto por:

- motor determinístico
- servicios de dominio
- memoria
- IA
- automatizaciones

La IA nunca controla directamente el negocio.

Siempre existe una capa determinística entre la IA y los datos críticos.

---

# ARQUITECTURA GENERAL

```
Usuario

↓

WhatsApp
Dashboard
Booking Engine
Instagram

↓

API

↓

Execution Engine

↓

Intent Detection

↓

Execution Router

↓

L0
L1
L2
L3
L4

↓

Business Services

↓

PostgreSQL (SSOT)

↓

Eventos

↓

Brains

↓

Respuesta
```

---

# PRINCIPIOS DE EJECUCIÓN

No toda solicitud necesita IA.

La mayoría debe resolverse mediante lógica determinística.

La IA únicamente participa cuando agrega valor.

---

# NIVELES DE EJECUCIÓN

## L0 — Determinístico

No utiliza LLM.

Responde mediante:

- reglas
- templates
- consultas SQL
- lookup
- cache

Tiempo esperado:

50–200 ms

Ejemplos:

- contraseña WiFi
- horarios
- políticas
- disponibilidad conocida
- estado de una reserva
- datos del huésped

---

## L1 — IA Simple

Utiliza modelos económicos.

Funciones:

- traducción
- reformulación
- clasificación
- extracción
- resumen corto

Tiempo objetivo:

< 1 segundo

---

## L2 — IA Contextual

Consulta:

- PostgreSQL
- Guest Brain
- Property Brain

Realiza:

- respuestas personalizadas
- propuestas
- contenido

---

## L3 — IA Analítica

Requiere múltiples fuentes.

Ejemplos:

- Revenue
- Forecast
- Competidores
- Marketing

---

## L4 — IA Estratégica

Solo para decisiones complejas.

Ejemplos:

- pricing estratégico
- optimización anual
- expansión
- recomendaciones globales

Debe utilizarse únicamente cuando el beneficio esperado supera ampliamente el costo computacional.

---

# FAST PATH

Toda consulta intenta resolverse primero mediante Fast Path.

```
Pregunta

↓

Rule Engine

↓

Cache

↓

Lookup

↓

Respuesta inmediata

↓

NO LLM
```

Solo si falla:

↓

Context Builder

↓

LLM

---

# EXECUTION ROUTER

Es el componente que decide:

- si usar IA
- qué modelo utilizar
- cuánto contexto enviar
- qué herramientas ejecutar

Nunca el usuario selecciona el modelo.

Siempre lo hace el sistema.

---

# MODEL ROUTER

Cada tarea utiliza el modelo más económico capaz de resolverla.

Ejemplo

FAQ

↓

Modelo pequeño

---

Resumen

↓

Modelo pequeño

---

Pricing

↓

Modelo avanzado

---

Forecast

↓

Modelo razonamiento

---

Esto reduce drásticamente el costo por propiedad.

---

# CONTEXT BUILDER

Construye únicamente el contexto necesario.

Nunca envía toda la base.

Orden de prioridad:

1 PostgreSQL

2 Reglas

3 Property Brain

4 Guest Brain

5 Growth Brain

6 Hospitality Network

---

# MEMORY HIERARCHY

Conversation Memory

↓

Guest Brain

↓

Property Brain

↓

Growth Brain

↓

Hospitality Knowledge Network

Cada nivel agrega contexto.

Nunca reemplaza al anterior.

---

# BUSINESS RULE ENGINE

Todas las reglas críticas viven aquí.

Ejemplos:

- cancelaciones

- reembolsos

- mínimos de estadía

- límites de pricing

- depósitos

- disponibilidad

- políticas

Nunca se implementan dentro del prompt.

---

# BUSINESS SERVICES

Cada dominio tiene su propio servicio.

Reservation Service

Pricing Service

Guest Service

Task Service

Content Service

Analytics Service

Payment Service

Notification Service

Todos son determinísticos.

---

# BDVL

Business Decision Validation Layer

Valida todas las acciones sensibles.

Clasificación:

Nivel 1

Sin riesgo

Ejecución automática.

---

Nivel 2

Riesgo bajo.

Registro en logs.

---

Nivel 3

Impacto económico.

Solicita confirmación.

---

Nivel 4

Riesgo crítico.

Nunca ejecuta automáticamente.

Ejemplos:

- reembolsos
- cancelaciones
- cambios financieros
- eliminación de reservas

---

# SINGLE SOURCE OF TRUTH

Única fuente de verdad:

PostgreSQL.

Nunca:

Embeddings

LLMs

Memorias

Caches

---

# EVENT ARCHITECTURE

Todo cambio genera un evento.

```
Reserva creada

↓

Transacción PostgreSQL

↓

Outbox

↓

Eventos

↓

Brains

↓

Analytics

↓

Automatizaciones
```

No existen procesos que modifiquen datos sin generar eventos.

---

# OUTBOX PATTERN

Toda escritura importante:

1 guarda el dato

2 guarda el evento

3 confirma transacción

4 publica evento

Esto evita inconsistencias.

---

# SEMANTIC CACHE

Antes de consultar un LLM:

↓

Buscar respuesta equivalente.

Si existe:

↓

Reutilizar.

↓

Costo cero.

---

# COST CONTROL

Cada propiedad posee un presupuesto mensual de IA.

Cada acción registra:

- modelo utilizado
- tokens
- costo
- duración

El sistema optimiza automáticamente.

---

# TOKEN STRATEGY

Prioridad absoluta:

Reducir llamadas al LLM.

Métodos:

Fast Path

Templates

Cache

Lookup

Compresión

Contexto mínimo

Model Router

---

# OBSERVABILIDAD

Toda ejecución registra:

usuario

modelo

duración

herramientas

tokens

agente

servicios

resultado

costo

---

# SEGURIDAD

Nunca un agente modifica directamente PostgreSQL.

Siempre utiliza:

Business Service

↓

BDVL

↓

Repositorio

↓

Base de datos

---

# MONOLITO MODULAR

Durante el MVP todo vive en una única aplicación.

Ventajas:

- menor costo
- menor latencia
- debugging simple
- despliegue sencillo

La separación es lógica.

No física.

---

# ESCALABILIDAD

Cuando aumente el volumen:

Se agregan:

Workers

Redis

Queues

Storage

CDN

Observabilidad

No cambia el dominio.

No cambian los servicios.

No cambia el modelo de datos.

---

# PRINCIPIOS DE RENDIMIENTO

Objetivos:

L0

<200 ms

L1

<1 s

L2

<2 s

L3

<5 s

L4

sin restricción estricta

---

# PRINCIPIOS ECONÓMICOS

Toda decisión arquitectónica debe responder:

¿Genera más valor del que cuesta?

Si la respuesta es no,

no debe ejecutarse con IA.

---

# VISIÓN

ATLAS no está construido alrededor de modelos de lenguaje.

Está construido alrededor de una arquitectura determinística que utiliza IA únicamente cuando produce una ventaja real.

Los modelos podrán cambiar.

Los proveedores podrán cambiar.

La arquitectura permanecerá estable.

Ese es el verdadero objetivo de este diseño.
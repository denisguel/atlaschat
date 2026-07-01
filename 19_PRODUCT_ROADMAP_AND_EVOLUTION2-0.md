````md
# ATLAS — Arquitectura de Datos y Memory System
## Documento 19 — Arquitectura de Datos, Memorias y Contexto
### Versión 1.0 | Aprobado por unanimidad por el Panel

---

# PROPÓSITO

ATLAS no utiliza una única memoria.

Utiliza un sistema de memorias especializadas donde cada una tiene un propósito distinto.

El objetivo es lograr simultáneamente:

- máxima velocidad
- mínima alucinación
- contexto profundo
- bajo costo
- aprendizaje continuo
- consistencia de negocio

La IA nunca es la fuente de verdad.

La memoria nunca reemplaza a la base de datos.

---

# PRINCIPIO FUNDAMENTAL

## PostgreSQL es la única fuente de verdad (SSOT)

Todo dato operativo existe únicamente en PostgreSQL.

Ejemplos:

- reservas
- huéspedes
- propiedades
- unidades
- pagos
- disponibilidad
- tareas
- conversaciones
- pricing
- competidores

Toda IA consulta primero PostgreSQL.

Las memorias únicamente ayudan a razonar.

Nunca deciden el estado operativo.

---

# LAS CINCO MEMORIAS DE ATLAS

```
                 PostgreSQL (SSOT)

                        │
        ─────────────────────────────────

              Memory Layer

      Conversation Memory
      Guest Brain
      Property Brain
      Growth Brain
      Network Intelligence

        ───────────────────────────────

             Context Builder

        ───────────────────────────────

                  LLM
```

---

# MEMORIA 1

## Conversation Memory

Objetivo:

recordar únicamente la conversación actual.

Contiene:

- últimos mensajes
- intención
- entidades detectadas
- acciones recientes
- contexto inmediato

Vida útil:

minutos u horas.

No se guarda indefinidamente.

---

Ejemplo

Usuario:

"¿Y para julio?"

ATLAS recuerda que anteriormente hablaban de:

Unidad 214

No necesita volver a preguntar.

---

# MEMORIA 2

## Guest Brain

Representa el conocimiento permanente del huésped.

No almacena únicamente datos.

Aprende comportamiento.

Ejemplos

- frecuencia de viaje

- anticipación promedio

- sensibilidad al precio

- historial de negociación

- destinos preferidos

- duración habitual

- temporadas favoritas

- contenido que generó conversión

- NPS histórico

- preferencias

- historial conversacional

---

No modifica reservas.

No modifica pagos.

No modifica disponibilidad.

Solo ayuda al razonamiento.

---

# MEMORIA 3

## Property Brain

Es completamente independiente para cada propiedad.

Aprende:

- pricing

- estacionalidad

- demanda

- clima

- competidores

- activos visuales

- performance de contenido

- comportamiento operativo

- decisiones del propietario

---

Ejemplo

El propietario rechaza diez veces una subida del 18%.

El sistema aprende.

En el futuro propondrá 10%.

---

No transfiere reglas automáticamente entre propiedades.

Cada propiedad mantiene su identidad.

---

# MEMORIA 4

## Growth Brain

No almacena operaciones.

Almacena conocimiento estratégico.

Ejemplos

- campañas

- conversiones

- atribución

- CTR

- engagement

- performance histórica

- ROI

- patrones de contenido

- oportunidades detectadas

---

Permite responder preguntas como:

¿Por qué cayó la conversión?

¿Qué campaña funcionó mejor?

¿Qué tipo de Reel convierte más?

---

# MEMORIA 5

## Hospitality Knowledge Network

Es la inteligencia colectiva.

Nunca comparte datos privados.

Solo comparte patrones agregados.

Ejemplo

"Noches de invierno con jacuzzi generan 18% más consultas."

No dice:

"LindaBay tuvo tres reservas."

Los datos privados permanecen aislados mediante RLS.

---

# MEMORIA OPERATIVA

Toda memoria puede ser eliminada.

La operación nunca.

Por eso:

Reserva

↓

PostgreSQL

↓

Evento

↓

Actualización de memoria

Nunca al revés.

---

# JERARQUÍA DE MEMORIAS

```
L0

Conversation

↓

L1

Guest Brain

↓

L2

Property Brain

↓

L3

Growth Brain

↓

L4

Hospitality Network
```

Cada nivel agrega contexto.

Nunca reemplaza al anterior.

---

# CONTEXT BUILDER

Es uno de los componentes más importantes de ATLAS.

Su función es construir el contexto exacto antes de consultar un LLM.

No envía toda la base de datos.

Selecciona únicamente la información necesaria.

---

Ejemplo

Pregunta:

"¿Subimos el precio del fin de semana?"

Context Builder consulta:

PostgreSQL

↓

unidad

↓

reservas

↓

ocupación

↓

competidores

↓

clima

↓

historial pricing

↓

Property Brain

↓

Guest Brain (si aplica)

↓

Prompt final

---

# PRIORIZACIÓN DEL CONTEXTO

Siempre sigue este orden:

1

Datos operativos (SSOT)

↓

2

Reglas determinísticas

↓

3

Property Brain

↓

4

Guest Brain

↓

5

Growth Brain

↓

6

Network Intelligence

---

Esto evita alucinaciones.

---

# COMPRESIÓN DE CONTEXTO

No toda la memoria se envía al modelo.

Se resume automáticamente.

Ejemplo

300 conversaciones

↓

Resumen

↓

Patrones

↓

Preferencias

↓

Contexto compacto

↓

LLM

---

Beneficios

- menos tokens

- menor costo

- mayor velocidad

- menos ruido

---

# MEMORIA SEMÁNTICA

Los embeddings sirven únicamente para recuperar información.

Nunca para decidir.

Ejemplo

Pregunta

"¿Qué pasó con la familia que vino en invierno?"

El embedding encuentra:

familia

↓

invierno

↓

jacuzzi

↓

NPS

↓

comentarios

Luego PostgreSQL valida los datos.

---

# ACTUALIZACIÓN DE MEMORIAS

Cada cambio genera un evento.

Ejemplo

Reserva creada

↓

Evento

↓

Outbox

↓

Guest Brain

↓

Property Brain

↓

Growth Brain

↓

Embeddings

Todo ocurre de forma asíncrona.

---

# CONSISTENCIA

Siempre existe un pequeño retraso entre:

PostgreSQL

y

Memoria semántica.

Para evitar errores:

Toda decisión consulta primero PostgreSQL.

La memoria solo complementa.

---

# VERSIONADO DEL CONTEXTO

Cada decisión importante guarda:

fecha

modelo utilizado

prompt

herramientas usadas

contexto

respuesta

resultado

Esto permite:

- auditoría

- aprendizaje

- debugging

- reproducibilidad

---

# DECISION GRAPH

Además del resultado se almacena:

qué información utilizó

qué reglas aplicó

qué agente decidió

qué herramientas consultó

qué resultado obtuvo

Con el tiempo se construye un grafo de decisiones único para cada propiedad.

Es uno de los principales moats de ATLAS.

---

# POLÍTICA DE RETENCIÓN

Conversation Memory

horas

Guest Brain

indefinido

Property Brain

indefinido

Growth Brain

indefinido

Eventos

indefinido

Logs técnicos

según configuración

---

# COSTOS

La memoria permanente utiliza principalmente PostgreSQL.

Los embeddings únicamente almacenan conocimiento útil.

No se vectoriza toda la base.

Solo:

documentación

conversaciones

descripciones

observaciones

contenido

comentarios

Esto mantiene bajo el costo.

---

# SEGURIDAD

Cada propietario accede únicamente a:

sus huéspedes

sus reservas

sus conversaciones

sus propiedades

La inteligencia colectiva utiliza únicamente información anonimizada.

Nunca existen accesos cruzados entre clientes.

---

# ROADMAP

## MVP

Conversation Memory

Guest Brain

Property Brain

Growth Brain

Embeddings básicos

---

## Fase 2

Context Builder avanzado

Compresión automática

Versionado completo

Decision Graph

---

## Fase 3

Hospitality Knowledge Network

Aprendizaje federado

Optimización automática del contexto

Memoria adaptativa

---

# PRINCIPIOS DEL MEMORY SYSTEM

- PostgreSQL siempre es la verdad.
- La IA nunca modifica directamente el estado operativo.
- Las memorias ayudan a razonar, no a decidir.
- Todo conocimiento nace de eventos reales.
- El contexto siempre se construye antes del razonamiento.
- Cada propiedad aprende de sí misma.
- La red comparte patrones, nunca datos privados.
- El sistema mejora con cada conversación, cada reserva y cada decisión aprobada.
````

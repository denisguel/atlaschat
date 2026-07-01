# 01_PRODUCT_PRINCIPLES.md

# ATLAS MASTER SPECIFICATION

**Documento:** 01_PRODUCT_PRINCIPLES.md
**Versión:** 1.0
**Estado:** Canonical

---

# PRODUCT PRINCIPLES

## Objetivo

Este documento define los principios arquitectónicos y filosóficos que gobiernan todas las decisiones técnicas y funcionales de ATLAS.

Estos principios tienen prioridad sobre cualquier decisión puntual de implementación.

Si una nueva funcionalidad contradice alguno de estos principios, deberá rediseñarse.

---

# PRINCIPIO 1 — AI Native

La Inteligencia Artificial no es un módulo adicional.

Es el núcleo del sistema.

Toda nueva funcionalidad deberá responder primero:

> "¿Puede un agente resolver esto?"

Si la respuesta es sí, la arquitectura deberá diseñarse alrededor del agente y no alrededor de una pantalla o formulario.

---

# PRINCIPIO 2 — Memory First

Toda interacción genera conocimiento.

Nunca debe descartarse información útil.

Ejemplos:

* conversaciones
* reservas
* preferencias
* horarios
* incidencias
* cancelaciones
* comentarios
* comportamientos
* decisiones tomadas
* recomendaciones aceptadas o rechazadas

Todo debe alimentar la memoria del sistema.

---

# PRINCIPIO 3 — Context Before Prompt

Los LLM nunca deben responder únicamente utilizando el prompt recibido.

Siempre deberán recibir contexto proveniente de uno o más Brain.

Fuentes posibles:

* Guest Brain
* Property Brain
* Growth Brain
* Booking History
* Pricing History
* Conversation History
* Owner Preferences
* System Events

La calidad del contexto determina la calidad de la respuesta.

---

# PRINCIPIO 4 — Event Driven

Todo cambio importante genera un evento.

Ejemplos:

ReservationCreated

ReservationCancelled

GuestArrived

GuestCheckedOut

ReviewReceived

PriceUpdated

ContentPublished

InvoiceGenerated

MaintenanceCreated

TaskCompleted

Todos los agentes reaccionan a eventos.

No dependen de consultas permanentes.

---

# PRINCIPIO 5 — Automation First

Toda tarea repetitiva debe ser automatizable.

Ejemplos:

* responder consultas
* enviar instrucciones
* recordar pagos
* crear tareas
* generar contenido
* actualizar precios
* generar reportes

Si una tarea requiere múltiples clics repetitivos, probablemente deba convertirse en un workflow.

---

# PRINCIPIO 6 — Human in the Loop

Toda automatización posee un nivel de autonomía.

## Nivel 1

IA recomienda.

Humano ejecuta.

## Nivel 2

IA propone.

Humano aprueba.

## Nivel 3

IA ejecuta automáticamente.

Cada workflow define explícitamente su nivel.

---

# PRINCIPIO 7 — WhatsApp First

WhatsApp constituye la interfaz primaria.

Todo proceso importante deberá poder iniciarse desde WhatsApp.

La interfaz web complementa la experiencia, pero no debe ser un requisito para operar el negocio.

---

# PRINCIPIO 8 — API First

Toda funcionalidad deberá estar disponible mediante APIs internas.

La interfaz nunca debe contener lógica crítica.

Frontend → API → Servicios → Base de datos.

Nunca:

Frontend → Base de datos directamente para lógica de negocio compleja.

---

# PRINCIPIO 9 — Stateless Agents

Los agentes no almacenan memoria propia.

Toda memoria persistente reside en:

* PostgreSQL
* Supabase Storage
* Vector Store (futuro)

Los agentes pueden detenerse y reiniciarse sin perder contexto.

---

# PRINCIPIO 10 — Single Source of Truth

Cada dato tiene un único origen oficial.

Ejemplos:

Disponibilidad:

→ Calendario interno.

Perfil del huésped:

→ Guest Brain.

Propiedad:

→ Property Brain.

Contenido:

→ Growth Brain.

Nunca deben existir dos fuentes oficiales para el mismo dato.

---

# PRINCIPIO 11 — Modularidad

Cada módulo debe poder evolucionar independientemente.

Ejemplos:

Revenue

Messaging

Marketing

Cleaning

Maintenance

Analytics

Payments

Cada uno debe tener responsabilidades claras y bajo acoplamiento.

---

# PRINCIPIO 12 — Escalabilidad Horizontal

El sistema debe funcionar inicialmente con:

1 propiedad

y crecer sin rediseño hasta:

miles de propiedades.

La arquitectura debe soportar:

* múltiples propietarios
* múltiples empresas
* múltiples regiones
* múltiples idiomas

---

# PRINCIPIO 13 — Cost Efficiency

El MVP debe minimizar costos.

Prioridades:

* herramientas gratuitas
* servicios serverless
* infraestructura bajo demanda
* evitar sobreingeniería

Solo optimizar cuando exista una necesidad demostrada.

---

# PRINCIPIO 14 — Security by Design

Toda funcionalidad debe diseñarse considerando:

* autenticación
* autorización
* auditoría
* trazabilidad
* cifrado
* backups
* protección de datos personales

La seguridad no es una etapa posterior.

---

# PRINCIPIO 15 — Explainability

Toda decisión automática relevante debe poder explicarse.

Ejemplo:

¿Por qué aumentó el precio?

↓

Porque:

* ocupación 90%
* fin de semana largo
* competencia agotada
* demanda creciente

La IA nunca debe comportarse como una caja negra para el propietario.

---

# PRINCIPIO 16 — Observability

Todo proceso importante genera:

* logs
* métricas
* eventos
* trazas

Cada automatización debe poder auditarse.

---

# PRINCIPIO 17 — Progressive Intelligence

ATLAS no nace completamente autónomo.

Aprende progresivamente.

Fase 1:

Asiste.

Fase 2:

Sugiere.

Fase 3:

Automatiza.

Fase 4:

Optimiza.

Fase 5:

Predice.

---

# PRINCIPIO 18 — MVP First

No desarrollar funcionalidades por anticipado.

Cada nueva capacidad debe cumplir al menos una de estas condiciones:

* aumenta ingresos
* reduce trabajo operativo
* mejora la experiencia del huésped
* desbloquea futuras capacidades

---

# PRINCIPIO 19 — Hospitality Native

Todas las decisiones deben responder a necesidades reales de la hospitalidad.

No construir funcionalidades genéricas si no generan valor específico para propietarios de alojamientos.

---

# PRINCIPIO 20 — Evolución Compatible

Toda nueva versión debe intentar mantener compatibilidad con:

* modelos de datos
* APIs
* workflows
* automatizaciones existentes

Cuando una ruptura sea inevitable, deberá documentarse mediante una decisión arquitectónica (ADR).

---

# Checklist para nuevas funcionalidades

Antes de aprobar una nueva funcionalidad, verificar:

* ¿Respeta AI Native?
* ¿Genera eventos?
* ¿Alimenta la memoria?
* ¿Puede automatizarse?
* ¿Es escalable?
* ¿Tiene API?
* ¿Es segura?
* ¿Es auditable?
* ¿Reduce trabajo del propietario?
* ¿Aporta valor al MVP?

Si alguna respuesta es negativa, la funcionalidad debe revisarse antes de implementarse.

---

**Fin del documento.**

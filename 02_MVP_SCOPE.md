# 02_MVP_SCOPE.md

# ATLAS MASTER SPECIFICATION

**Documento:** 02_MVP_SCOPE.md
**Versión:** 1.0
**Estado:** Canonical

---

# 1. Objetivo del MVP

El MVP (Minimum Viable Product) de ATLAS debe demostrar una hipótesis muy concreta:

> **Un propietario de alquileres temporarios puede operar la mayor parte de su negocio desde WhatsApp, asistido por IA, reduciendo trabajo operativo y aumentando la conversión de reservas.**

El objetivo del MVP **no es** competir con un PMS enterprise, sino validar el modelo AI-Native con usuarios reales.

---

# 2. Hipótesis a validar

Al finalizar el MVP debemos poder responder con datos a las siguientes preguntas:

### H1 — Operación

¿El propietario dedica menos tiempo por propiedad?

**KPI**

* Horas ahorradas por semana.
* Cantidad de tareas automatizadas.

---

### H2 — Conversión

¿La IA mejora la conversión de consultas en reservas?

**KPI**

* Conversión WhatsApp → Reserva.
* Tiempo medio de respuesta.
* Tasa de abandono.

---

### H3 — Contenido

¿El propietario publica más contenido sin esfuerzo adicional?

**KPI**

* Publicaciones generadas.
* Alcance.
* Consultas originadas.

---

### H4 — Gestión

¿Toda la información del negocio puede centralizarse?

**KPI**

* Cantidad de herramientas reemplazadas.
* Tiempo para encontrar información.

---

# 3. Perfil del usuario objetivo

## Primera etapa

Propietarios independientes.

Características:

* 1 a 20 propiedades.
* Poco tiempo disponible.
* Sin equipo de trabajo.
* Utilizan WhatsApp constantemente.
* Manejan múltiples plataformas manualmente.

---

## Segunda etapa

Administradores.

20–100 propiedades.

---

## Tercera etapa

Empresas de administración.

Más de 100 propiedades.

---

# 4. Funcionalidades obligatorias (Must Have)

Estas funcionalidades son imprescindibles para considerar completo el MVP.

---

## 4.1 Autenticación

Debe incluir:

* Registro.
* Inicio de sesión.
* Recuperación de contraseña.
* Roles básicos.

---

## 4.2 Gestión de propiedades

Cada propiedad deberá permitir:

* nombre
* descripción
* ubicación
* amenities
* fotos
* reglas
* capacidad
* check-in
* check-out
* políticas

---

## 4.3 Calendario

Visualización de disponibilidad.

Eventos:

* reservas
* bloqueos
* mantenimiento

---

## 4.4 Gestión de huéspedes

Cada huésped debe poseer un perfil persistente.

Información mínima:

* nombre
* teléfono
* email
* idioma
* historial
* preferencias
* notas

Este perfil constituye el inicio del Guest Brain.

---

## 4.5 Reservas

Crear.

Modificar.

Cancelar.

Consultar.

Historial completo.

---

## 4.6 Inbox de WhatsApp

El propietario podrá:

* leer mensajes
* responder
* delegar respuestas a IA
* aprobar respuestas
* dejar responder automáticamente

---

## 4.7 Guest Brain

Debe almacenar:

* historial
* conversaciones
* preferencias
* reservas
* observaciones
* score interno

---

## 4.8 Property Brain

Debe almacenar:

* información estructurada
* reglas
* manuales
* documentos
* fotografías
* preguntas frecuentes

---

## 4.9 Agente Conversacional

Debe responder consultas como:

Disponibilidad.

Precios.

Ubicación.

Servicios.

Políticas.

Check-in.

Mascotas.

Estacionamiento.

Preguntas frecuentes.

---

## 4.10 Dashboard

Indicadores mínimos:

* ocupación
* reservas
* ingresos
* conversaciones
* consultas
* automatizaciones ejecutadas

---

## 4.11 Automatizaciones

Primeros workflows:

Nueva consulta.

Nueva reserva.

Reserva cancelada.

Check-in.

Check-out.

Solicitud de reseña.

Recordatorio de pago.

Mensaje pre llegada.

Mensaje post salida.

---

## 4.12 Panel de configuración

Configuración de:

* propietario
* empresa
* IA
* WhatsApp
* idioma
* moneda
* horarios
* automatizaciones

---

# 5. Funcionalidades deseables (Should Have)

No bloquean el lanzamiento.

* Revenue Assistant.
* Generación automática de contenido.
* Calendario inteligente.
* Detección de oportunidades.
* Reportes IA.
* Etiquetado automático.
* Clasificación de huéspedes.
* Búsqueda semántica.

---

# 6. Funcionalidades futuras (Could Have)

No forman parte del MVP.

* Channel Manager.
* Dynamic Pricing avanzado.
* Motor de reservas propio.
* Facturación electrónica.
* Marketplace de servicios.
* Voice AI.
* MCP Hotel.
* Integraciones hoteleras HTNG.
* Multiempresa avanzada.
* App móvil nativa.
* Predictive Revenue Engine.

---

# 7. Funcionalidades explícitamente excluidas

El MVP NO incluye:

* Contabilidad.
* Nómina.
* Gestión hotelera compleja.
* POS.
* Restaurante.
* Housekeeping avanzado.
* RMS completo.
* Fidelización enterprise.
* BI avanzado.

---

# 8. Criterios de aceptación

El MVP será considerado exitoso cuando un propietario pueda:

* Registrar una propiedad.
* Registrar un huésped.
* Crear una reserva.
* Consultar disponibilidad.
* Operar desde WhatsApp.
* Obtener respuestas automáticas.
* Acceder al historial del huésped.
* Gestionar conversaciones.
* Ejecutar automatizaciones.
* Ver indicadores básicos.

Sin depender de herramientas externas para esas tareas.

---

# 9. Restricciones técnicas

El MVP debe utilizar:

Frontend:

* Next.js
* React
* TypeScript
* TailwindCSS

Backend:

* Supabase

Base de datos:

* PostgreSQL

Automatización:

* n8n

IA:

* Proveedor configurable mediante API

Hosting:

* Vercel
* Supabase

---

# 10. Restricciones económicas

Objetivo:

Mantener el costo operativo extremadamente bajo durante la validación.

Se priorizarán:

* software open source
* servicios gratuitos
* serverless
* pago por uso

---

# 11. Definición de MVP terminado

El MVP estará completo cuando un propietario pueda operar diariamente su negocio utilizando ATLAS como herramienta principal y la IA participe activamente en la atención al huésped, la organización de la información y la automatización de procesos.

El éxito del MVP se medirá por la reducción del trabajo operativo y la mejora en la velocidad y calidad de respuesta, no por la cantidad de funcionalidades implementadas.

---

**Fin del documento.**

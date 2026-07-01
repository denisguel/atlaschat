# ATLAS_SPEC_REPO

# ATLAS – AI-Native Hospitality Operating System (LATAM)

**Versión:** 1.0  
**Estado:** Canonical Specification  
**Autor:** Denis Gueler + ChatGPT  
**Propósito:** Single Source of Truth del proyecto

---

# ¿Qué es ATLAS?

ATLAS (AI-Native Hospitality Operating System) es un sistema operativo inteligente diseñado para propietarios independientes y pequeñas empresas de alquileres temporarios en Latinoamérica.

A diferencia de un PMS tradicional, ATLAS utiliza Inteligencia Artificial como componente nativo para operar el negocio, automatizar tareas, asistir en decisiones y aumentar la conversión de reservas.

El objetivo es que un propietario pueda administrar todo su negocio desde una conversación de WhatsApp, mientras un conjunto de agentes especializados ejecuta las operaciones necesarias.

---

# Visión

Construir el sistema operativo de referencia para la hospitalidad independiente en LATAM, donde la IA no sea una función adicional sino el núcleo de todo el producto.

---

# Principios del producto

- AI First
- WhatsApp First
- Mobile First
- Cloud Native
- API First
- Modular
- Escalable
- Bajo costo de operación
- Orientado al MVP
- Preparado para arquitectura multiagente

---

# Stack tecnológico

Frontend

- Next.js
- React
- TypeScript
- TailwindCSS

Backend

- Supabase
- PostgreSQL
- Edge Functions
- Storage
- Auth

Automatización

- n8n

IA

- LLM Provider (configurable)
- Sistema multiagente

Canales

- WhatsApp
- Email
- Sitio web
- Instagram (roadmap)
- Booking Engine

---

# Componentes principales

## Guest Brain

Memoria persistente del huésped.

Contiene:

- preferencias
- historial
- conversaciones
- reservas
- comportamiento
- score
- segmentación

---

## Property Brain

Conocimiento completo de cada propiedad.

Incluye:

- amenities
- reglas
- fotos
- precios
- disponibilidad
- manuales
- mantenimiento
- inventario

---

## Growth Brain

Memoria de marketing.

Incluye:

- campañas
- contenidos
- métricas
- atribución
- rendimiento
- SEO
- redes sociales

---

# Agentes

ATLAS opera mediante agentes especializados.

Inicialmente:

- Revenue Agent
- Conversion Agent
- Content Agent
- Operations Agent
- Guest Relations Agent

Todos coordinados por:

- Orchestrator Agent

---

# Objetivos del MVP

El MVP debe permitir:

- administrar propiedades
- gestionar reservas
- responder WhatsApp automáticamente
- centralizar huéspedes
- generar contenido
- sugerir precios
- registrar eventos
- ejecutar workflows
- operar desde una sola interfaz

---

# Roadmap resumido

Fase 1

- MVP funcional

Fase 2

- Multi-propiedad

Fase 3

- Multiempresa

Fase 4

- Marketplace

Fase 5

- MCP Hospitality

---

# Repositorio

Este README acompaña los siguientes documentos:

- 00_PRODUCT_VISION.md
- 01_PRODUCT_PRINCIPLES.md
- 02_MVP_SCOPE.md
- 03_SYSTEM_ARCHITECTURE.md
- 04_AI_ARCHITECTURE.md
- 05_DATABASE.md
- 06_SUPABASE_SCHEMA.sql
- 07_AGENT_SPEC.md
- 08_BRAIN_SPEC.md
- 09_N8N_FLOWS.md
- 10_API_SPEC.md
- 11_FRONTEND.md
- 12_SECURITY.md
- 13_DEPLOYMENT.md
- 14_TESTING.md
- 15_ROADMAP.md
- 16_INVESTOR.md

---

# Estado del proyecto

Este repositorio constituye la documentación oficial de ATLAS.

Toda implementación deberá respetar las especificaciones aquí definidas.

Las decisiones arquitectónicas futuras deberán extender esta documentación sin romper compatibilidad con el MVP.
# ATLAS — Índice Maestro del Proyecto
## Documento 21 — Master Index, Relaciones y Navegación
### Versión 1.0 | Documento Maestro

---

# PROPÓSITO

Este documento actúa como punto único de entrada al proyecto ATLAS.

Define:

- estructura documental
- dependencias
- orden de lectura
- relaciones entre documentos
- alcance de cada módulo

No contiene especificaciones funcionales.

Su objetivo es facilitar la navegación del proyecto.

---

# DOCUMENTOS DEL PROYECTO

## 00 — Índice Maestro

Visión general del proyecto.

---

## 01 — Visión Estratégica

Propósito.

Misión.

Problema.

Oportunidad.

Propuesta de valor.

Moats.

---

## 02 — Producto

Descripción funcional completa.

Capacidades.

Usuarios.

Casos de uso.

---

## 03 — Arquitectura Técnica

Arquitectura general.

Backend.

Frontend.

IA.

Servicios.

Dashboard.

---

## 04 — Base de Datos

Modelo relacional.

Tablas.

Relaciones.

RLS.

Eventos.

---

## 05 — Arquitectura de Agentes

Orchestrator.

Guest.

Revenue.

Operations.

Content.

Growth.

BDVL.

---

## 06 — Dashboard

Pantallas.

Componentes.

Widgets.

UX.

Navegación.

---

## 07 — Revenue Intelligence

Pricing.

Forecast.

Competidores.

Optimización.

---

## 08 — Guest Experience

Conversaciones.

Personalización.

Automatizaciones.

Guest Brain.

---

## 09 — Growth Engine

Marketing.

Contenido.

Conversión.

Atribución.

---

## 10 — Automatizaciones

N8N.

Triggers.

Workflows.

Integraciones.

---

## 11 — Integraciones

WhatsApp.

Instagram.

OTAs.

Pagos.

Calendarios.

---

## 12 — Seguridad

Autenticación.

Autorización.

RLS.

Logs.

Auditoría.

---

## 13 — KPIs

North Star.

Revenue.

Operación.

Conversión.

Experiencia.

---

## 14 — Roadmap de Producto

Sprints.

Prioridades.

Backlog.

Versiones.

---

## 15 — Arquitectura de IA

Modelos.

Prompting.

Context Builder.

LLMs.

Razonamiento.

---

## 16 — Costos y Unit Economics

Infraestructura.

Tokens.

IGPPP.

Rentabilidad.

---

## 17 — Estrategia Competitiva

Moats.

Posicionamiento.

Mercado.

Escalabilidad.

---

## 18 — Direct Booking Engine

Motor de reservas.

Landing.

SEO.

Checkout.

Conversión.

---

## 19 — Arquitectura de Datos y Memory System

SSOT.

Brains.

Embeddings.

Context Builder.

Decision Graph.

---

## 20 — Roadmap Tecnológico

Escalabilidad.

Evolución.

Fases.

Protocol Rails.

---

## 21 — Índice Maestro

Relaciones.

Dependencias.

Mapa documental.

---

## 22 — Prompt Maestro de Construcción

Prompt utilizado para generar el MVP.

---

## 23 — Framework AHLA / HTNG

Alineación con estándares de la industria.

Protocol Rails.

Discoverability.

MCP.

---

## 24 — Sprint Maestro

Plan completo de ejecución.

Orden recomendado.

Entregables.

---

## 25 — Datos Iniciales

Carga inicial.

LindaBay.

Competidores.

Configuración.

---

# DEPENDENCIAS ENTRE DOCUMENTOS

```
01
 │
02
 │
03
 │
├──────────────┐
│              │
04             05
│              │
├──────┐       │
│      │       │
06     10      15
│      │       │
│      └──┐    │
│         │    │
07        11   │
│         │    │
├─────────┴────┤
│              │
08             09
│              │
└──────┬───────┘
       │
      18
       │
      19
       │
      20
       │
      23
       │
      24
       │
      25
```

---

# FLUJO GENERAL DEL SISTEMA

```
Usuario

↓

WhatsApp / Dashboard

↓

Orchestrator

↓

Agente especializado

↓

BDVL

↓

Servicios

↓

PostgreSQL

↓

Eventos

↓

Brains

↓

Respuesta
```

---

# RELACIÓN ENTRE MÓDULOS

Revenue utiliza:

- Competidores
- Pricing
- Calendario
- Forecast

---

Guest utiliza:

- Conversaciones
- Reservas
- Guest Brain
- WhatsApp

---

Growth utiliza:

- Instagram
- Booking Engine
- Analytics
- Conversión

---

Operations utiliza:

- Limpieza
- Check-in
- Tareas
- Calendario

---

Content utiliza:

- Story Engine
- Guest Brain
- Property Brain
- Growth Brain

---

# CAPAS DEL SISTEMA

## Presentación

Dashboard

WhatsApp

Landing Pages

Booking Engine

---

## Aplicación

Orchestrator

Agentes

Servicios

Automatizaciones

---

## Dominio

Reservas

Pricing

Revenue

Marketing

Operación

---

## Persistencia

PostgreSQL

Storage

Logs

Embeddings

---

## Inteligencia

Guest Brain

Property Brain

Growth Brain

Network Intelligence

---

# PRINCIPIOS DOCUMENTALES

Cada documento debe cumplir:

- un único propósito
- responsabilidades claras
- sin duplicar especificaciones
- referencias cruzadas
- independencia relativa

---

# CONVENCIONES

Todos los documentos utilizan:

- Markdown
- Terminología consistente
- Inglés técnico cuando corresponde
- Español para negocio
- Versionado semántico

---

# NOMENCLATURA

Prefijo:

ATLAS_

Número:

00–25

Nombre descriptivo

Ejemplo:

ATLAS_03_Arquitectura_Tecnica.md

---

# ORDEN RECOMENDADO DE LECTURA

1. Visión
2. Producto
3. Arquitectura
4. Base de datos
5. Agentes
6. Dashboard
7. Revenue
8. Guest
9. Growth
10. Automatizaciones
11. Integraciones
12. Seguridad
13. KPIs
14. Roadmap
15. IA
16. Costos
17. Estrategia
18. Booking Engine
19. Memory System
20. Roadmap Tecnológico
21. Índice Maestro
22. Prompt Maestro
23. AHLA / HTNG
24. Sprint Maestro
25. Datos Iniciales

---

# MANTENIMIENTO

Cada modificación importante del proyecto debe reflejarse en el documento correspondiente.

El Índice Maestro debe mantenerse actualizado para preservar la coherencia documental.

Este documento no reemplaza ningún otro.

Su función es servir como mapa permanente de toda la arquitectura documental de ATLAS.
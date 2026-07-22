# ADR-003-B: Enmienda — Telegram como Puente hacia WhatsApp (Gate de Salida F5)

**Status:** Accepted
**Date:** 2026-07-21
**Amends:** ADR-003 (WhatsApp as Primary Interface), ADR-003-A (Telegram Interim Channel Amendment)
**Deciders:** Denis Gueler (founder)

<!-- NOTA F0 (2026-07-21): Este documento se registra bajo ADR-003-B, NO bajo "ADR-011" como
lo nombra ATLAS_PLAN_MAESTRO_SAAS.md §11 y el pedido original de la tarea F0. Motivo:
ADR-011 ya existe y está Accepted desde 2026-07-18 (ADR-011_CLAUDE_AS_PRIMARY_AI_PROVIDER.md,
"Claude como Proveedor de IA Primario") — es una decisión cerrada y citada en CLAUDE.md como
tal. Crear un segundo documento reclamando el mismo ID es exactamente la clase de colisión
que esta tarea (F0) existe para prevenir, no para producir.
Además, el contenido de esta decisión NO es una decisión nueva independiente: es la respuesta
al ítem "A revisar" que ADR-003-A dejó abierto explícitamente ("Criterio de disparo para
migrar a WhatsApp"). Registrarla como enmienda de la cadena ADR-003 (siguiendo el patrón ya
usado por ADR-010-B/C/D, ADR-011-A) es más preciso que un ADR nuevo, y no colisiona con nada.
El texto de Contexto/Decisión de abajo es el aprobado por Denis en el Plan Maestro §11, sin
modificar — solo se corrigió el ID y el encabezado a formato de enmienda. Pendiente: corregir
las 5 referencias a "ADR-011" en ATLAS_PLAN_MAESTRO_SAAS.md (líneas 7, 143, 229, 231, 248) a
"ADR-003-B" — no se tocó ese archivo en F0 por estar fuera de los 3 entregables de esta tarea. -->

## Context

ADR-003 estableció WhatsApp como interfaz primaria del producto. ADR-003-A formalizó Telegram
como canal interino de Phase 1 por fricción operativa de la API de WhatsApp Business (aprobación
de Meta, verificación de negocio), dejando abierto un ítem explícito: *"Criterio de disparo para
migrar a WhatsApp (¿número de propiedades? ¿feedback explícito de usuarios sobre preferencia de
canal?)"*.

WhatsApp-first (ADR-003) sigue vigente como destino por penetración de mercado en LATAM. Telegram
se desplegó como puente por velocidad de iteración (webhook trivial, sin aprobación Meta, costo
cero).

## Decision

Se supersede parcialmente ADR-003 en cuanto al *timing* (no a la tesis de mercado, que sigue
intacta): Telegram es un canal **transitorio y soportado**, no un desvío a corregir de inmediato.
La arquitectura de canal debe ser una capa fina sin lógica de negocio, para que la migración a
WhatsApp sea un cambio de transporte, no de lógica.

**Condición de salida** (resuelve el ítem abierto de ADR-003-A): el gate de la Fase F5 del
programa — paridad funcional en WhatsApp con la misma suite de invariantes que corre hoy sobre
Telegram, y LindaBay migrado. Telegram permanece como canal secundario soportado
post-migración, no se descontinúa.

## Consequences

**Facilita:**
- Resuelve el ítem abierto de ADR-003-A con un criterio verificable (suite de invariantes en
  verde), no una fecha arbitraria ni una corazonada.
- Mientras la abstracción de canal (ADR-003-A, ADR-007, ADR-008) se respete, la migración de F5
  no debería requerir reescritura de lógica de negocio — es la prueba de que esa abstracción se
  hizo bien.

**Dificulta:**
- Ninguna nueva respecto de ADR-003-A. La disciplina de no acoplar lógica de negocio a la API de
  Telegram sigue siendo el prerequisito, no algo que este documento agregue.

**A revisar:**
- Proveedor de WhatsApp Business API (Meta Cloud API directo vs. BSP) — diferido a un RFC corto
  al entrar a F5, no decidido acá.

## Action Items
1. [ ] Al entrar a F5: RFC corto de proveedor de WhatsApp Business API.
2. [ ] Verificar que la suite de invariantes que correrá en paralelo Telegram/WhatsApp durante
   la migración esté definida antes de declarar el gate cumplido.
3. [ ] Corregir en `ATLAS_PLAN_MAESTRO_SAAS.md` las 5 referencias a "ADR-011" (línea 7 —
   encabezado, línea 143 — §7 F0, líneas 229/231 — §11, línea 248 — §13) para que apunten a
   ADR-003-B.

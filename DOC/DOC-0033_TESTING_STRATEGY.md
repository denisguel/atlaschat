# DOC-0033: Testing Strategy — Fase 1

**Status:** Accepted | **Referencias:** DOC-0031 §6, ARCH-0021 §21, ADR-010-C

## Tests obligatorios antes de cualquier deploy a producción

| # | Test | Cubre |
|---|---|---|
| 1 | Aislamiento de lectura multi-tenant (2 orgs de prueba, verificar que Org A no lee datos de Org B en ninguna tabla) | DOC-0031 §6.1 |
| 2 | Aislamiento de escritura multi-tenant | DOC-0031 §6.2 |
| 3 | Scraper de competidores respeta `property_id` explícito en query (no solo RLS) | DOC-0031 §6.3, bug ya corregido |
| 4 | BDVL: acción CRITICAL nunca ejecuta sin `approved_by` seteado | ARCH-0022 §14 |
| 5 | BDVL: acción LOW ejecuta sin bloqueo | ARCH-0022 §11 |
| 6 | Fast Path L0: consulta rutinaria (WiFi, horarios) responde sin invocar LLM | ARCH-0021 §17 |
| 7 | Latencia L1-L2 <2s en condiciones normales | ARCH-0021 §21 |
| 8 | `execution_logs` registra cost_usd en cada llamada IA (sin excepción) | ARCH-0027 §22 |
| 9 | Graceful degradation: al superar budget cap, sistema baja de proveedor sin error visible al usuario | ARCH-0027 §21, ADR-010-C |
| 10 | Piso de contexto mínimo: Revenue Agent recibe datos de propiedad+competidores+histórico en cada prompt | ADR-010-B |

## No bloqueante para Fase 1 (diferido)

- Testing de carga/escala (no aplica con 1 propiedad de validación)
- Testing de Multi-Agent orchestration (no existe en Fase 1)
- Testing de memoria vectorial/semántica (no existe en Fase 1)

## Herramientas

- Unit/integration: Vitest o Jest (Next.js estándar)
- RLS: queries directas vía dos service clients autenticados como orgs distintas

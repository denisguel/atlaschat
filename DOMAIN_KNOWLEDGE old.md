# DOMAIN_KNOWLEDGE.md — Cómo funciona el negocio (no cómo está hecho el software)

**Fecha:** 2026-07-11
**Estado:** Canonical — lectura OBLIGATORIA antes de construir cualquier feature de pricing/revenue
**Por qué existe:** los 60 documentos describen la ARQUITECTURA del software. Ninguno describe CÓMO FUNCIONA EL NEGOCIO. Sin esto, el sistema construye software correcto que resuelve el problema equivocado. Este documento es el conocimiento de dominio que el propietario transfirió y que el corpus nunca capturó.

**Regla para quien construya sobre esto:** si una decisión de implementación no está cubierta acá, PREGUNTAR al propietario. No inventar reglas de negocio. No asumir prácticas de hotelería genérica que no apliquen a STR independiente en Costa Atlántica.

---

# PARTE 1 — EL NEGOCIO: ALQUILER TEMPORARIO EN COSTA ATLÁNTICA (ARGENTINA)

## 1.1 Qué tipo de negocio es (y qué NO es)

**ES:** alquiler temporario de unidades independientes (departamentos, monoambientes, dúplex) en destinos de playa con **estacionalidad extrema**. El propietario típico tiene 1-5 unidades, vive en otra ciudad (Buenos Aires), y gestiona a distancia.

**NO ES:** un hotel. No hay recepción, no hay tarifa diaria uniforme, no hay RevPAR clásico como métrica principal. Aplicar lógica de revenue management hotelero sin adaptar es el error #1.

**Diferencias críticas vs. hotelería tradicional:**
| Hotel | STR Costa Atlántica |
|---|---|
| Tarifa por noche, todo el año | Tarifa por noche EN TEMPORADA BAJA; por PAQUETE/SEMANA en alta |
| Estadía promedio 1-3 noches | Estadía promedio 7+ noches en alta, 2-4 en baja |
| Ocupación pareja | Ocupación 90-100% en enero, 10-30% en junio |
| Inventario grande, decisiones estadísticas | 1-5 unidades: cada reserva mueve la aguja materialmente |
| Precio en moneda estable | Precio en un contexto de inflación/dólar volátil |

## 1.2 Estructura de temporadas (Costa Atlántica — Mar de las Pampas)

**Temporadas ALTA (modelo PAQUETE — se vende por semana o estadía completa, NO por noche):**
- **Diciembre post-20** (desde ~20/12): semanal
- **Navidad/Año Nuevo**: paquete propio, tarifa premium
- **Enero**: semanal (pico absoluto del año)
- **Febrero**: semanal (alto, ligeramente menor que enero)
- **Marzo hasta mediados**: semanal (decreciente)
- **Semana Santa**: paquete (fecha móvil, cambia cada año)
- **Vacaciones de invierno (julio)**: paquetes de 4 noches Y de 7 noches (el huésped elige)
- **Fines de semana largos** (feriados): paquete del finde completo (típicamente 3 noches)

**Temporadas BAJA/MEDIA (modelo POR NOCHE, con L-J vs V-D diferenciado):**
- **Diciembre pre-20**: por noche, SIN mínimo de 7 (importante: es alta demanda pero sin restricción de semana completa)
- **Resto del año** (abril, mayo, junio, agosto, septiembre, octubre, noviembre): por noche
- Cada mes tiene sus propios valores, distintos entre sí
- Dentro de cada mes: **L-J una tarifa, V-D otra** (el fin de semana vale más)

**Implicancia crítica para el sistema:** una consulta de precio NO se resuelve igual según la fecha. Preguntar "cuánto sale el 10 de enero" (paquete semanal, mínimo 7 noches) es una operación distinta a "cuánto sale el 10 de octubre" (por noche, L-J).

## 1.3 Mínimos de estadía (min_nights)

- Alta temporada (dic post-20 a marzo): **mínimo 7 noches** (semana completa, típicamente sábado a sábado o domingo a domingo)
- Diciembre pre-20: **sin mínimo**
- Vacaciones de invierno: mínimos de 4 o 7 según el paquete
- Findes largos: el paquete completo (3 noches típico)
- Resto del año: sin mínimo o mínimo 2 noches (fin de semana)

## 1.4 El contexto macro argentino (ESTO ES ÚNICO Y CRÍTICO)

**Por qué el pricing en Argentina no se puede copiar de manuales de revenue management internacionales:**

1. **Doble moneda:** el costo del propietario (servicios, sueldos, mantenimiento) es en pesos e inflaciona. El ingreso ideal es en dólares (protección). El huésped local piensa en pesos, el extranjero en dólares. **USD es la moneda canónica de cómputo del sistema; ARS es display.**

2. **Ciclos de acople/desacople:** el dólar puede estar "barato" (como ahora, mediados 2026) mientras los costos internos suben. Eso comprime el margen del propietario y CAMBIA el precio óptimo — no se puede solo mirar el histórico en USD sin entender en qué ciclo estaba.

3. **TECHO DE PRECIO POR DESTINO ALTERNATIVO (concepto central, no está en ningún manual):**
   El precio máximo de una unidad en Costa Atlántica NO lo define la competencia local. Lo define el **costo de un viaje alternativo comparable** — típicamente **Brasil**.
   - Si una semana en Mar de las Pampas cuesta lo mismo (o más) que una semana en Brasil (con vuelo, todo incluido), **el huésped se va a Brasil**.
   - Ese es el techo real. Superarlo = unidad vacía, sin importar lo que digan los competidores locales.
   - **El sistema debe permitir configurar este techo por propiedad** (cada propietario conoce su alternativa relevante: Brasil, otro destino nacional, etc.)

4. **Poder adquisitivo doméstico:** el turismo de Costa Atlántica es mayoritariamente argentino, de clase media. Si los sueldos reales caen, la demanda cae aunque los precios en USD parezcan baratos.

## 1.5 De dónde sale la información de competencia

| Fuente | Qué provee | Cómo se accede |
|---|---|---|
| **Booking.com** | Precios de competidores por fecha, disponibilidad, rating, reviews | Scraping (el sistema ya tiene scraper base) |
| **lindabay.com.ar** (web de la administración del complejo) | Tarifas de otras unidades del MISMO complejo — el competidor más directo. Tiene un **motor de reservas con pricing dinámico** (botón "Reservas") | Scraping indirecto del motor de reservas, consultando fechas |
| Otros complejos de la zona | Referencia de mercado | Booking principalmente |

**Nota:** el complejo LindaBay tiene ~130 propiedades independientes. Los vecinos son competencia directa Y son el primer mercado de clientes del SaaS.

## 1.6 Comparabilidad de competidores (concepto que ningún manual cubre)

**Dos unidades NO son comparables 1:1 aunque estén en el mismo complejo.** Un propietario experimentado sabe ajustar:

- Un studio de 65m² vs. uno de 60m² → el más grande justifica **~+9%**
- Un 2 ambientes de 69m² vs. uno menor → **+12-15%**
- Otros factores que el propietario pondera: vista, piso, estado, amenities, ubicación dentro del complejo

**Implicancia:** al comparar contra un competidor, el sistema debe:
1. Saber a qué unidad propia corresponde ese competidor (`applies_to_unit`)
2. Conocer el ajuste de comparabilidad (`adjustment_note`) que el propietario define
3. **NUNCA inventar m² o características que no tenga cargadas** — si no sabe, compara crudo y lo aclara

---

# PARTE 2 — REVENUE MANAGEMENT: QUÉ SEÑALES IMPORTAN Y CÓMO SE DECIDE

## 2.1 Las decisiones que toma un propietario (el trabajo que ATLAS automatiza)

1. **¿Subo, bajo o mantengo el precio de estas fechas?**
2. **¿Acepto esta reserva o espero una mejor?** (una estadía corta que bloquea una semana en alta es pérdida)
3. **¿Cómo lleno este gap?** (2 noches sueltas entre dos reservas)
4. **¿Ya debería estar vendido para esta fecha, o voy tarde?** (booking pace)
5. **¿Cuánto pierdo si dejo el precio como está?** (costo de oportunidad)

## 2.2 Señales que informan esas decisiones

### INTERNAS (de los datos del propietario)
| Señal | Qué es | Por qué importa |
|---|---|---|
| **Ocupación actual** | % de noches vendidas para un período | Alta ocupación con anticipación → subir precio. Baja → bajar o promocionar |
| **Booking pace / pickup** | Ritmo de reservas vs. mismo período de años anteriores | Si a 60 días de enero tengo 40% vendido y el año pasado tenía 70%, voy TARDE → acción |
| **Lead time** | Cuánta anticipación reserva la gente | En alta, se reserva con 3-6 meses. En baja, con días. Define cuándo actuar |
| **Gap nights** | Noches sueltas entre reservas | Difíciles de vender. Requieren precio agresivo o mínimo flexible |
| **Estadía promedio** | Noches por reserva | Si baja en alta temporada, hay un problema (gente no toma la semana completa) |
| **Cancelaciones** | Tasa y timing | Alta tasa de cancelación tardía = riesgo de quedar vacío sin tiempo de revender |
| **Histórico de precio/ocupación** | Qué se cobró y qué se llenó, por temporada | Base de la estacionalidad. **Ojo: ajustar por el ciclo del dólar de cada año** |

### EXTERNAS (del mercado)
| Señal | Qué es | Por qué importa |
|---|---|---|
| **Precios de competencia por fecha** | Qué cobran los comparables ESA fecha | Referencia directa. Si todos subieron y yo no, dejo plata. Si todos bajaron, me quedo vacío |
| **Disponibilidad de competencia** | Cuántos están llenos | Si el complejo está lleno y yo tengo lugar → puedo subir. Si todos vacíos → problema de demanda, no de precio |
| **Techo por destino alternativo** | Costo de Brasil/alternativa | Límite superior absoluto (§1.4) |
| **Eventos locales** | Festivales, feriados, eventos | Aumentan demanda puntual |
| **Clima** | Pronóstico | Impacta reservas de último minuto en playa |
| **Ciclo macro (dólar, poder adquisitivo)** | Contexto argentino | Modula todo lo anterior |

## 2.3 Lógica de decisión de precio (cómo piensa un revenue manager)

**El precio óptimo NO es "el más alto posible". Es el que maximiza REVENUE TOTAL (precio × ocupación), no precio unitario.**

Marco de decisión:

```
1. ¿En qué temporada cae la fecha? → determina modelo (noche/paquete) y tarifa base
2. ¿Cuál es mi ocupación actual para esa fecha?
   - Alta (>80%) + anticipación → SUBIR (hay demanda, capturar más)
   - Media (40-80%) → MANTENER o ajuste fino según pace
   - Baja (<40%) + fecha cerca → BAJAR o promocionar (mejor vender barato que no vender)
3. ¿Cómo viene el booking pace vs. años anteriores?
   - Adelantado → puedo subir
   - Atrasado → acción correctiva urgente
4. ¿Qué hace la competencia comparable ESA fecha? (ajustada por comparabilidad)
   - Todos más caros que yo → tengo espacio para subir
   - Todos más baratos → riesgo de quedarme afuera
5. ¿Estoy cerca del techo (Brasil/alternativa)?
   - Sí → NO subir aunque los otros factores lo sugieran
6. ¿Es un gap night?
   - Sí → precio agresivo, es mejor vender barato que dejar vacío
7. DECISIÓN: subir X%, bajar X%, o mantener — SIEMPRE con justificación explícita
```

**Regla de oro:** una noche vacía es ingreso perdido para siempre (inventario perecedero). Pero bajar el precio de toda la temporada por llenar 2 noches destruye el revenue. El arte está en el ajuste selectivo.

## 2.4 KPIs relevantes (adaptados a STR, no hotel)

**PRIMARIOS (los que el propietario mira):**
| KPI | Fórmula | Por qué |
|---|---|---|
| **Revenue total del período** | Σ (precio × noches vendidas) | Lo que importa al final |
| **Tasa de ocupación** | Noches vendidas / noches disponibles | Salud del negocio |
| **ADR (Average Daily Rate)** | Revenue / noches vendidas | Precio promedio real obtenido |
| **RevPAN (Revenue per Available Night)** | Revenue / noches disponibles | **El más importante**: combina precio y ocupación. Sube si vendés más caro O más noches |

**SECUNDARIOS (diagnóstico):**
| KPI | Qué diagnostica |
|---|---|
| **Booking pace** (reservas acumuladas vs. mismo momento año anterior) | ¿Voy adelantado o atrasado? |
| **Lead time promedio** | ¿Con cuánta anticipación compran? Define cuándo actuar |
| **Gap nights ratio** | % de noches sueltas no vendidas |
| **Tasa de cancelación** | Riesgo de revenue |
| **Estadía promedio (ALOS)** | ¿Se respetan los mínimos? |
| **% reservas directas vs. OTA** | Cada reserva directa ahorra 15-18% de comisión — **es margen puro** |

**KPI ESTRATÉGICO (el que justifica que ATLAS exista):**
- **Revenue incremental vs. escenario sin ATLAS**: si el propietario cobraba $X y con las recomendaciones cobra $X+20% con la misma ocupación, eso es el valor entregado.

## 2.5 Direct booking: por qué es el objetivo estratégico

- Booking.com/Airbnb cobran **15-18% de comisión**
- Una reserva directa de $1000 deja $1000. La misma vía OTA deja ~$830
- **Cada reserva directa que ATLAS genera es margen puro para el propietario**
- Esto es lo que hace que ATLAS pague su suscripción con 1-2 reservas directas al mes

---

# PARTE 3 — CÓMO DEBE COMPORTARSE EL SISTEMA (reglas operativas)

## 3.1 Al recomendar un precio, SIEMPRE:

1. **Resolver la temporada correcta** (paquete vs. noche, L-J vs V-D, min_nights)
2. **Traer competencia comparable de ESA fecha** (no un promedio genérico), ajustada por comparabilidad
3. **Verificar techo** (destino alternativo)
4. **Considerar ocupación actual y booking pace** de ese período
5. **Explicar el razonamiento**: "recomiendo $X porque [temporada], [competencia real con nombre y link], [ocupación], [pace]"
6. **Nunca auto-aplicar**: propone, el propietario decide (BDVL)

## 3.2 Guardrails de conocimiento (qué NO puede hacer el sistema)

- **NUNCA inventar tarifas** que no estén cargadas o derivadas de histórico real
- **NUNCA inventar características de competidores** (m², ambientes) que no tenga
- **NUNCA aplicar lógica de hotel** (tarifa diaria uniforme) a temporadas de paquete
- **NUNCA recomendar por encima del techo** sin marcarlo explícitamente
- Si falta información para una decisión → **decirlo, no adivinar** ("no tengo histórico de esta temporada, baja confianza")

## 3.3 Datos que el sistema YA TIENE y debe usar (no pedirlos de nuevo)

- `pricing_history`: histórico real cargado por el propietario (3 temporadas, con tipo de cambio por año)
- `competitor_scraper_results`: 10 competidores reales con `applies_to_unit`, `adjustment_note`, `source_url`
- `reservations`: ocupación real
- `units`: capacidad, para filtrar por pax

**Regla crítica:** antes de pedirle al propietario que cargue algo, VERIFICAR si el dato ya existe en la base. La queja principal del propietario es que se le pide información que ya transfirió.

## 3.4 Interacción esperada (cómo se siente usar ATLAS)

**MAL (lo que NO debe ser):**
> Propietario: "precio para 4 personas en enero"
> Sistema: "/pricing requiere: unidad, fecha inicio, fecha fin"

**BIEN (lo que debe ser):**
> Propietario: "precio para 4 personas en enero"
> Sistema: "Para enero (temporada alta, paquete semanal, mínimo 7 noches), tenés 2 unidades para 4 personas:
> • UF214 (cap 4): semana $700. Competencia comparable: LINDA STUDIO 60 (link) a $750 — está 7% arriba, pero es 5m² más grande, así que en términos ajustados estamos parejos.
> • UF508 (cap 6): semana $1000. LINDA 205 DUPLEX (link) a $1100.
> Tu ocupación de enero está en 45% a 5 meses vista; el año pasado a esta altura tenías 70%. **Vas atrasado.**
> Sugerencia: mantener UF214 y bajar UF508 a $950 para acelerar. ¿Querés que analicemos alternativas?"
>
> Propietario: "¿y si dejo el duplex en 1000?"
> Sistema: [RECUERDA el contexto anterior] "Si mantenés $1000 con el pace actual, proyectando..., podrías terminar enero con ~65% de ocupación en vez de ~85%. Revenue estimado: $X vs $Y. La diferencia es ~$Z."

**Elementos obligatorios de una buena interacción:**
- Entiende lenguaje natural, no comandos
- **Mantiene el hilo de la conversación** (memoria de contexto)
- Muestra competencia con nombre + precio + link (verificable)
- Da contexto de pace/ocupación, no solo el número
- Permite negociar/explorar ("¿y si...?")
- Explica el porqué, brevemente

---

# PARTE 4 — LO QUE FALTA DEFINIR

**Ya definido por el propietario** (ver Parte 5): gap nights por temporada, booking pace (arranca sep/oct), objetivo = maximizar ingresos.

**Pendiente:**
1. **Tarifas reales por temporada** — se INFIEREN del histórico ya cargado + scraping; el propietario confirma. NO cargar a mano.
2. **El techo concreto** (§1.4): ¿costo de semana equivalente en Brasil? ¿o % sobre competencia local? → configurable por propiedad.
3. **Elasticidad de precio:** se estima del histórico propio (§5.3). Si no alcanza, el sistema declara baja confianza — nunca inventa.

---

# PARTE 5 — LÓGICA OPERATIVA DE REVENUE (umbrales y reglas concretas)

**Por qué esta parte existe:** el corpus define conceptos ("gap night optimization", "booking pace") pero nunca los umbrales operativos. Sin ellos, el sistema improvisa reglas inventadas. Esta parte los fija con el criterio del propietario.

## 5.1 Gap nights — la definición depende de la TEMPORADA (no es un número fijo)

**Insight crítico del propietario:** un "gap" no es un número universal de noches. Es una vacancia que **no puede venderse bajo las reglas de esa temporada**. Como el mínimo de estadía cambia por temporada, el gap cambia con él.

| Temporada | Qué constituye un gap | Acción sugerida |
|---|---|---|
| **Baja/media** (per_night) | **1 noche suelta** ya es un gap | Ofrecer a huéspedes de la reserva anterior o posterior (extender su estadía). Es la vía más barata: ya están ahí, cero costo de adquisición |
| **Finde largo** (paquete 3 noches) | Si alguien reserva solo 2 de las 3 noches → queda 1 noche huérfana | Ofrecer extensión al huésped del finde, o vender suelta con precio agresivo |
| **Vacaciones de invierno** (paquetes 4/7) | **2 noches** sueltas = gap (no llegan al paquete mínimo de 4) | Precio agresivo o flexibilizar mínimo |
| **Alta temporada** (paquete 7 noches) | **3-4 noches** = gap (no completan la semana mínima) | El más costoso. Opciones: flexibilizar el mínimo para esas fechas, precio agresivo, o aceptar la pérdida si falta mucho tiempo (puede llegar una semana completa) |

**Regla operativa:**
1. Detectar vacancias entre reservas confirmadas
2. Clasificar según la temporada de esas fechas (tabla arriba)
3. **Primera acción SIEMPRE: ofrecer extensión a huéspedes adyacentes** (anterior o posterior). Costo de adquisición = 0
4. Si no funciona: precio agresivo (el propietario define el % — punto de partida sugerido: 15-25% según cercanía de la fecha)
5. **Cuanto más cerca la fecha, más agresivo** — una noche vacía es ingreso perdido para siempre

**Guardrail:** en alta temporada, NO flexibilizar el mínimo de 7 noches con mucha anticipación (puede llegar una semana completa que vale más). Flexibilizar solo cuando la fecha se acerca y el riesgo de vacancia total es alto.

## 5.2 Booking pace — cómo se mide y cuándo alarma

**Cuándo empieza a venderse la alta temporada:** desde **septiembre/octubre** (3-4 meses antes de enero). Ese es el arranque de la curva.

**Cómo calcular el pace:**
```
pace_actual = noches vendidas para [período target] al día de hoy
pace_referencia = noches vendidas para [mismo período] a la misma distancia temporal, en años anteriores
                  (de pricing_history + reservations históricas)

ratio = pace_actual / pace_referencia
```

**Interpretación:**
| Ratio | Diagnóstico | Acción |
|---|---|---|
| > 1.15 | **Adelantado** — se vende más rápido que el histórico | Oportunidad de SUBIR precio: hay más demanda que la esperada |
| 0.85 – 1.15 | **En línea** | Mantener, ajuste fino |
| 0.60 – 0.85 | **Atrasado** | Revisar precio vs. competencia. Considerar bajar o promocionar |
| < 0.60 | **Muy atrasado — alerta** | Acción correctiva urgente: bajar precio, promocionar, revisar si hay problema de mercado (no solo de precio) |

**Ajuste por cercanía:** el mismo ratio significa cosas distintas según cuánto falte:
- A 4+ meses: un pace atrasado no es alarmante todavía (hay tiempo)
- A 2 meses: atrasado = actuar ya
- A 3 semanas: atrasado = precio agresivo, es la última ventana

**Importante:** comparar contra el histórico del MISMO ciclo económico cuando sea posible. Un enero con dólar caro no es comparable a uno con dólar barato (§1.4).

## 5.3 El objetivo: MAXIMIZAR INGRESOS (no ocupación, no precio)

**Definición del propietario, y es la correcta:** el objetivo NO es llenar a cualquier precio, ni sostener el precio a costa de quedar vacío. **Es maximizar el revenue total.**

```
Revenue = precio × noches vendidas
```

**Implicancia para el sistema:** si bajar el precio genera suficiente ocupación adicional como para que el revenue total suba, **bajar es la recomendación correcta.** Y viceversa.

**Configurable por propietario:** el sistema debe permitir que cada propietario elija su estrategia:
- `maximize_revenue` (default, recomendado)
- `maximize_occupancy` (llenar, aunque el revenue sea menor — útil para propietarios que priorizan reviews/actividad)
- `maximize_rate` (sostener precio, aceptar vacancia — útil para propiedades premium)

**Cómo el sistema evalúa "maximizar ingresos":**
1. Estimar la elasticidad: ¿cuánta ocupación adicional genera bajar X%?
   - **Fuente inicial:** histórico propio (si bajé 10% en el pasado, ¿cuánto más vendí?)
   - **Si no hay histórico suficiente:** usar competencia y pace como proxy, y **decirlo explícitamente** ("estimación de baja confianza, sin histórico de elasticidad")
2. Proyectar revenue en cada escenario (mantener / subir X% / bajar X%)
3. Recomendar el de mayor revenue proyectado
4. **Mostrar la proyección al propietario** (no solo el número final)

**Guardrail:** NUNCA inventar una elasticidad. Si no hay datos para estimarla, decirlo y presentar escenarios con supuestos explícitos ("si bajar 10% aumentara la ocupación 20%, el revenue subiría $X — pero no tengo histórico para confirmar esa elasticidad").

## 5.4 Revenue Simulator / What-if (RFC-0008 §28, operacionalizado)

**Qué debe simular:**
| Escenario | Proyecta |
|---|---|
| Mantener precio actual | Ocupación esperada (según pace + histórico), revenue proyectado |
| Subir X% | Ocupación esperada (menor), revenue proyectado |
| Bajar X% | Ocupación esperada (mayor), revenue proyectado |

**Salida esperada (lo que ve el propietario):**
```
Si mantenés $1000/semana: ~65% ocupación proyectada → revenue ~$X
Si bajás a $950: ~85% ocupación proyectada → revenue ~$Y  ← mayor
Si subís a $1100: ~45% ocupación proyectada → revenue ~$Z

Recomiendo bajar a $950. Diferencia vs. mantener: +$[Y-X]
```

**Supuestos obligatorios a declarar:** de dónde sale la estimación de ocupación (histórico propio / pace actual / competencia), y su nivel de confianza.

## 5.5 Explicabilidad (Principio 15, operacionalizado)

**Toda recomendación de precio DEBE contener:**
1. **Temporada y modelo** aplicado ("Enero, paquete semanal, mínimo 7 noches")
2. **Competencia comparable de esa fecha**, con nombre + precio + link, ajustada por comparabilidad
3. **Ocupación actual** del período
4. **Booking pace** vs. histórico ("vas 30% atrasado respecto al año pasado a esta altura")
5. **La decisión**: subir/bajar/mantener + **el porqué en 1-2 frases**
6. **Proyección de impacto** (§5.4) si el propietario quiere profundizar
7. Si hay techo cerca (§1.4), **marcarlo explícitamente**

**Prohibido:** dar un número sin las razones. El propietario no aprueba un precio, aprueba una estrategia.

## 5.6 Bandas de autonomía (BDVL) — CUÁNDO SÍ y CUÁNDO NO

Propuesta original: "la IA ajusta automáticamente si el cambio es <5% y confianza >95%".

**Veredicto del panel: NO todavía. Peligroso antes de validar.**

- El propietario aún no confía en el motor (con razón — está recién construido)
- Auto-aplicar precios sin supervisión, con un motor no validado, puede destruir revenue real
- **Regla:** bandas de autonomía se habilitan SOLO después de que el propietario haya validado N recomendaciones manualmente y confíe en el criterio del sistema

**Cuándo habilitarlas (criterio):**
- El propietario aprobó ≥20 recomendaciones y el sistema acertó (la ocupación/revenue resultante fue el proyectado)
- Recién entonces: permitir auto-aplicar cambios menores (<5%) con confianza alta, y SIEMPRE notificando ("ajusté el precio de X a Y automáticamente, podés revertirlo")
- Cambios grandes, temporada alta, o baja confianza → SIEMPRE aprobación humana

Esto cumple Principio 17 (Progressive Intelligence) sin arriesgar el negocio del propietario.

---

# PARTE 6 — MEJORAS FUTURAS (Fase 3+, registradas, NO construir ahora)

Del análisis de mejoras "10/10" — valiosas pero requieren componentes que no existen:

| Mejora | Requiere | Fase |
|---|---|---|
| Simulación cruzada precio + marketing | Growth Brain | 3 |
| Gap night → evento → Growth Agent genera post automático | Growth Agent + Event Bus | 3 |
| Pricing personalizado por LTV del huésped | Guest Brain. **Nota: evaluar implicancias de discriminación de precios antes de implementar** | 3 |
| **Inteligencia externa vía MCP (AirDNA, clima, eventos)** | **Alineado con ADR-012 — es el moat agéntico** | **2** |
| Explicabilidad visual (curva de reserva actual vs. proyectada) | Dashboard | 2 |
| Bandas de autonomía | Validación previa del motor (§5.6) | 2-3, con criterio |

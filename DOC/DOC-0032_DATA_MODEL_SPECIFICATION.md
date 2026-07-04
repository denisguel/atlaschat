# DOC-0032: Data Model Specification — Fase 1

**Status:** Accepted | **Fecha:** 2026-07-03
**Referencias:** DOC-0011 (Database Architecture), DOC-0031 (Security & Multi-Tenancy), ADR-010/010-B/010-C/010-D, ARCH-0022 (BDVL)

## Principio

RLS obligatorio en toda tabla desde la primera migración (DOC-0031 §3). `organization_id` presente aunque Fase 1 tenga un solo tenant real — evita reescritura en Fase 2.

## Esquema

```sql
-- Tenant raíz
organizations (
  id uuid PK,
  name text,
  created_at timestamptz
)

-- Propiedades (LindaBay = 1 fila)
properties (
  id uuid PK,
  organization_id uuid FK -> organizations,
  name text,
  location jsonb,           -- lat/lng, ciudad, complejo
  attributes jsonb,         -- amenities, temporada, reglas
  display_currency text default 'ARS',
  created_at timestamptz
)

-- Unidades (LindaBay = 2 filas)
units (
  id uuid PK,
  property_id uuid FK -> properties,
  name text,
  capacity int,
  base_price_usd numeric,   -- USD canónico, memoria del proyecto
  created_at timestamptz
)

-- Huéspedes — perfil estructurado simple, NO Guest Brain vectorial (ADR-010)
guests (
  id uuid PK,
  organization_id uuid FK -> organizations,
  name text,
  phone text,
  email text,
  language text default 'es',
  notes text,
  created_at timestamptz
)

-- Reservas
reservations (
  id uuid PK,
  unit_id uuid FK -> units,
  guest_id uuid FK -> guests,
  check_in date,
  check_out date,
  status text check (status in ('inquiry','tentative','confirmed','checked_in','checked_out','cancelled')),
  price_usd numeric,
  source text,              -- direct, telegram, referral
  created_at timestamptz
)

-- Historial de precios (piso de contexto minimo, ADR-010-B)
pricing_history (
  id uuid PK,
  unit_id uuid FK -> units,
  date date,
  price_usd numeric,
  occupancy_rate numeric,
  source text check (source in ('manual','ai_recommendation')),
  created_at timestamptz
)

-- Resultados de scraper de competidores (bug de multi-tenancy ya corregido — RLS estricto acá)
competitor_scraper_results (
  id uuid PK,
  property_id uuid FK -> properties,   -- RLS por property_id, NUNCA global
  competitor_name text,
  scraped_date date,
  price_usd numeric,
  source_url text,
  created_at timestamptz
)

-- Decisiones BDVL (ADR-010-C, ARCH-0022) — piso de reglas, no servicio pesado
bdvl_decisions (
  id uuid PK,
  organization_id uuid FK -> organizations,
  action_type text,
  risk_level text check (risk_level in ('low','medium','high','critical')),
  proposal jsonb,
  decision text check (decision in ('approved','rejected','requires_confirmation','requires_approval')),
  approved_by uuid null,
  created_at timestamptz
)

-- Content performance (ADR-010-D) — moat de datos, empieza dia 1
content_assets (
  id uuid PK,
  property_id uuid FK -> properties,
  hook text,
  format text,
  channel text,
  posted_at timestamptz,
  season text
)

content_performance (
  id uuid PK,
  content_asset_id uuid FK -> content_assets,
  bookings_attributed int default 0,
  revenue_attributed_usd numeric default 0
)

-- Two-tier API keys (DOC-0031 §4)
property_integrations (
  id uuid PK,
  property_id uuid FK -> properties,
  provider text,             -- telegram, mercadopago, resend
  encrypted_credentials jsonb,
  created_at timestamptz
)

-- Observabilidad de costo IA (ARCH-0027 §22, obligatorio desde dia 1)
execution_logs (
  id uuid PK,
  organization_id uuid FK -> organizations,
  execution_level text check (execution_level in ('L0','L1','L2','L3','L4')),
  provider text,             -- claude, groq, etc
  tokens_input int,
  tokens_output int,
  cost_usd numeric,
  latency_ms int,
  created_at timestamptz
)
```

## RLS — aplicar a TODAS las tablas sin excepción

```sql
alter table properties enable row level security;
alter table units enable row level security;
alter table guests enable row level security;
alter table reservations enable row level security;
alter table pricing_history enable row level security;
alter table competitor_scraper_results enable row level security;
alter table bdvl_decisions enable row level security;
alter table content_assets enable row level security;
alter table content_performance enable row level security;
alter table property_integrations enable row level security;
alter table execution_logs enable row level security;

-- Policy base (ejemplo, repetir patrón por tabla vía organization_id o property_id):
create policy tenant_isolation on properties
  for all using (organization_id = auth.jwt() ->> 'organization_id');
```

## Checklist pre-código (DOC-0031 §3, aplicar por tabla antes de merge)
- [ ] RLS enabled + policy de SELECT/INSERT/UPDATE/DELETE
- [ ] Test con 2 tenants simultáneos (aislamiento verificado)
- [ ] Si alimentada por proceso background (scraper) — filtro explícito en query, no solo RLS

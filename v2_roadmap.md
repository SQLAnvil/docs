# SQLAnvil V2 Roadmap

Post-V1 (BigQuery + Postgres/Supabase first-class) feature backlog. Captured as a checklist;
each item has notes so it can be picked up later without re-deriving context.

## 1. Additional database support

- [ ] **MySQL / MariaDB** (committed — do first).
- [ ] **Research the next tier**, splitting operational (OLTP) vs warehouse (OLAP/analytical):
  - *Operational:* SQLite (great for local/dev + tests), SQL Server (T-SQL), Oracle, CockroachDB
    (Postgres wire-compatible — may be cheap), DuckDB (embedded analytical, also a file-query
    engine — overlaps item 2).
  - *Warehouse:* Snowflake, Amazon Redshift, ClickHouse, Databricks / Spark SQL, Trino/Presto,
    Amazon Athena, Microsoft Fabric.
- **What each adapter costs** (the pattern is established by `postgres`/`supabase`): a
  `cli/api/dbadapters/<db>.ts` + `<db>_execution_sql.ts`, a `<Db>Connection` proto +
  `credentials.ts` validation, a `<Db>Options` action config block in `protos/configs.proto`,
  dialect-specific DDL/DML in the execution SQL generator, a CLI branch on `warehouse`, and a
  docker integration fixture + `tests/integration/<db>.spec.ts`.
- **Decision to make:** how much warehouse-agnostic SQL generation can be shared vs. per-dialect.
  MySQL/MariaDB differ from Postgres on: identifier quoting (backticks vs `"`), upsert
  (`INSERT ... ON DUPLICATE KEY UPDATE` vs `ON CONFLICT`), no materialized views (emulate?),
  no `CREATE INDEX ... INCLUDE`, different partitioning syntax, no transactional DDL.

## 2. File-based source support (CSV, Parquet, JSON, …)

- [ ] Model file sources as a first-class concept (likely a declaration/source variant or a new
      action type that resolves to a queryable relation).
- **Why it's uneven across warehouses:** BigQuery has this built in (external/federated tables,
  `LOAD DATA`, wildcard URIs over GCS). Postgres does **not** out of the box — options:
  - `file_fdw` (CSV/text, server-side files only),
  - `COPY ... FROM` (load into a real table; needs a target schema),
  - `parquet_fdw` / `pg_parquet` extensions (Parquet),
  - DuckDB as an embedded query engine for Parquet/CSV/JSON (could double as item 1).
- **Decision to make:** one warehouse-agnostic `source: { format, location }` surface that each
  adapter implements its own way, vs. per-warehouse blocks. Consider cloud object storage
  (S3/GCS/Azure) URIs, not just local paths.

## 3. Dataform → SQLAnvil conversion script

- [ ] A migration tool that converts an existing Dataform project to SQLAnvil.
- **Scope (mirrors the deltas in the main repo's `AGENTS.md` / the
  `sqlanvil-engineering-fundamentals` skill — reuse that delta list as the conversion spec):**
  - `dataform.json` → `workflow_settings.yaml` (drop `defaultProject`/`defaultLocation` for
    non-BigQuery; `dataformCoreVersion` → `sqlanvilCoreVersion`).
  - `@dataform/core` → `@sqlanvil/core`; `.df-credentials.json` shape mapping per target warehouse.
  - When targeting Postgres: translate `bigquery: { partitionBy, clusterBy }` → `postgres: {
    partition, indexes }` where possible; flag what can't auto-translate (e.g. BQ-specific
    `OPTIONS`, `bigqueryPolicyTags`).
  - Leave BigQuery-targeted projects mostly intact (just the rename/core swap).
- **Decision to make:** lossy auto-translate with warnings vs. a report-only "what needs manual
  attention" mode (probably offer both).

## 4. Improved `init` (interactive Q&A scaffolding)

- [ ] Replace/augment `sqlanvil init` with an interactive question/answer flow.
- **Flow:** pick warehouse (bigquery/postgres/supabase/…) → collect connection params → generate a
  valid `workflow_settings.yaml`, a `.df-credentials.json` **(strict JSON — no comment keys; the
  parser rejects them)**, a starter `definitions/` (a declaration/source + one model), and a
  `.gitignore` that excludes credentials.
- **Lessons to bake in** (learned building `examples/postgres_shop`): set `sqlanvilCoreVersion`
  correctly (not `dataformCoreVersion`); for Postgres use the flat `PostgresConnection` field names
  (`host/port/database/user/password/sslMode/defaultSchema`); offer to test the connection
  (the CLI already has a credentials test path).
- Ties into design doc Phase 4 ("`sqlanvil init` templates for both new warehouses").

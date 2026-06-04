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

## 5. `--environment` (named environments)

- [ ] Add a first-class **`--environment <name>`** concept for dev/staging/prod isolation.
- **What already exists (the primitive, don't rebuild it):** `--schema-suffix <name>` (and
  `datasetSuffix:` in `workflow_settings.yaml`) already produce dynamic datasets. The suffix is
  applied **in core at compile time** — `session.ts` appends `_<suffix>` to every action's
  `target.schema` before any adapter runs — so it **already works for Postgres/Supabase** (the
  adapter just reads `target.schema`). `--vars` overrides flow the same way, via
  `constructProjectConfigOverride` → `projectConfigOverride` (`cli/index.ts`). What's missing is
  the ergonomic, named layer on top.
- **The new feature:** `--environment` is the name that actually makes sense (more intuitive than
  `--schema-suffix`). Two possible scopes:
  - **A — thin alias:** `--environment <name>` simply sets `schemaSuffix = <name>`. A few lines in
    the CLI option layer; delivers exactly "append the environment to the schema". Good stopgap.
  - **B — real environments (preferred):** an `environments.json` (or `environments:` block in
    `workflow_settings.yaml`) defining named environments, each carrying its own `schemaSuffix`,
    `vars`, and — the real reason environments exist — **its own credentials / database**
    (prod vs staging usually differ by more than a schema suffix); optionally a git ref.
    `--environment staging` loads that env and applies its overrides (config override + credential
    selection).
- **Implementation pointers:** CLI flag → merge env overrides into the existing
  `projectConfigOverride` plumbing; per-env credentials means selecting the credentials file/profile
  by environment (today `--credentials` is a single path). Keep `--schema-suffix` as the documented
  low-level primitive (and possibly make `--environment` an alias that also sets it).
- **Decision to make:** ship A first as a stopgap, or go straight to B. Where do environments live —
  separate `environments.json` (Dataform's legacy location) or a block in `workflow_settings.yaml`
  (more consistent with the rest of sqlanvil config)?

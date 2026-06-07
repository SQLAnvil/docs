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

## 2. Import/export at the warehouse edges — files, object storage, and cross-platform (DB↔DB)

A project/run stays **single-warehouse** — multiple live warehouse connections in one project is
explicitly **not** wanted (adapters stay isolated). Everything internally happens on one DB; this
item is about the **edges**: getting data *in* (sources) and pushing results *out* (sinks), whether
the other end is a **file / object store** or **another database**. The two transports share a
type-translation layer.

### 2a. Files & object storage (CSV / Parquet / JSON)

- [ ] Model a file as a first-class **source** (a `declaration`/source variant or a new action type
      that resolves to a queryable relation) and the reverse for **export** (sink).
- **Why it's uneven:** BigQuery has it built in — external/federated tables, `LOAD DATA`, wildcard
  `gs://…` URIs for **read**, and `EXPORT DATA OPTIONS(uri = 'gs://…', format = 'JSON'|'CSV'|'PARQUET')
  AS SELECT …` for **write** — all in a plain operation (working example: the acuantia project's
  `definitions/operations/gemstone_ai/op_export_gemstone_product_jsonl.sqlx`). Postgres has **no**
  native `gs://`/file I/O out of the box — options: `file_fdw` (server-side CSV), `COPY … FROM/TO`
  (incl. `… PROGRAM` to stream to/from object storage), `parquet_fdw` / `pg_parquet`, a Supabase
  Wrapper/FDW, or DuckDB (`httpfs`) as an embedded bridge for Parquet/CSV/JSON on S3/GCS/Azure.
- **V2 goal:** a warehouse-agnostic `{ format, location }` surface (`gs://`/`s3://`/local URIs) each
  adapter implements its own way — specifically a **Postgres/Supabase equivalent** of BigQuery's
  native object-storage read **and** write.

### 2b. Cross-platform DB↔DB movement (with type translation)

- [ ] An import/export action (or `declaration`/sink variant) that reads from / writes to a
      *secondary database* — BigQuery ↔ Postgres ↔ Supabase.
- **Today (no dedicated feature):** warehouse-native federation — BigQuery federated queries reading
  Postgres/Supabase, or Supabase Wrappers / Postgres FDW reading BigQuery (see
  `hybrid_warehouses_supabase_bigquery.md`, Patterns A/C) — plus sequential separate runs for
  multi-platform outputs (Pattern B). Works, but manual and warehouse-specific. **Named connections
  (shipped in core 1.2.0)** already cover the *read* direction for FDW-reachable sources; this item
  is the first-class, type-aware generalization (incl. the write/export direction).
- Secondary-platform credentials live alongside the primary `.df-credentials.json` (import/export
  only — not a second execution engine).

### Shared & out of scope

- **Type translation** (both transports): BigQuery `STRUCT`/`ARRAY` ↔ Postgres `jsonb`/arrays,
  `NUMERIC`/`BIGNUMERIC` ↔ `numeric`, `TIMESTAMP` semantics, Supabase `vector`, etc.
- **Out of scope — consumer file-sync (Dropbox, Google Drive, …):** not queryable object stores;
  supporting them is a separate *fetch connector* (API auth → download → load), not in-place access.
- **Decisions:** model the edges as new action types vs. an external `sqlanvil import/export` CLI;
  one warehouse-agnostic `{ format, location }`/connection surface vs. per-warehouse blocks; how far
  to push automatic type translation vs. requiring explicit casts.

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

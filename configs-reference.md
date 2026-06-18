# Configs Reference

SQLAnvil actions are configured at two levels:

1. **Project-level** — `workflow_settings.yaml` at the root of your project (committed), defining the **warehouse** and global defaults, plus the gitignored **`.df-credentials.json`** that holds the connection (secrets).
2. **Action-level** — the `config {}` block in SQLX files, or the action entry in `actions.yaml`, defining per-action behavior (type, dependencies, schema, warehouse-specific options).

For the complete proto-generated field reference (all action config fields), see [Configs Proto Reference](/reference/configs).

---

## `workflow_settings.yaml` — project config (committed, secret-free)

`warehouse:` is a **flat string** — `bigquery` (the implicit default if omitted), `postgres`, `supabase`, or `mysql`. Defaults are flat keys alongside it. **The connection — host, password, etc. — is NOT here; it lives in the gitignored `.df-credentials.json`** so secrets never land in version control.

```yaml
warehouse: postgres                       # bigquery | postgres | supabase | mysql
defaultDataset: analytics                 # default schema (PG/Supabase), dataset (BQ), or database (MySQL)
defaultAssertionDataset: sqlanvil_assertions
sqlanvilCoreVersion: 1.7.0                # sqlanvil's own version line
vars:
  env: production
# BigQuery-only defaults:
# defaultProject: my-gcp-project
# defaultLocation: US
```

BigQuery is the implicit warehouse when `warehouse:` is omitted, so it's the only one you can leave unset; `postgres`/`supabase`/`mysql` set it explicitly.

### `.df-credentials.json` — the connection (gitignored)

Strict JSON, no comments. Shape depends on the warehouse:

```jsonc
// Postgres / Supabase — PostgresConnection
{
  "host": "db.example.com",
  "port": 5432,
  "database": "analytics",
  "user": "sqlanvil_writer",
  "password": "…",
  "sslMode": "require",          // disable | require | verify-ca | verify-full
  "defaultSchema": "public"
}
```
```jsonc
// MySQL / MariaDB — MysqlConnection (NOTE: no defaultSchema; "schema" is the database)
{
  "host": "localhost",
  "port": 3306,
  "database": "analytics",
  "user": "root",
  "password": "",
  "sslMode": "disable"           // disable | require
}
```
```jsonc
// BigQuery — a {projectId, location, credentials} wrapper, or omit to use gcloud ADC
{
  "projectId": "my-gcp-project",
  "location": "US",
  "credentials": { /* service-account key JSON */ }
}
```

### `environments:` — named environments (`--environment`)

Define dev/staging/prod once and select with `--environment <name>` (on `compile`/`run`/`test`). Each environment bundles non-secret overrides **plus a pointer to its own gitignored credentials file** — secrets stay out of the committed config.

```yaml
environments:
  dev:
    schemaSuffix: dev                      # → output schemas get a _dev suffix
    credentials: .df-credentials.dev.json
  prod:
    defaultDatabase: prod_db
    vars:
      region: us-prod
    credentials: .df-credentials.prod.json
```

Per-environment fields: `schemaSuffix`, `vars`, `defaultDatabase`, `defaultLocation`, `credentials` (path, relative to the project dir). **Precedence: explicit CLI flag > environment > `workflow_settings` defaults** (`vars` merge per-key). `--schema-suffix` remains the low-level primitive. Gitignore the per-env files (`init` ignores `.df-credentials*.json`). See [Named Environments](/environments).

### `connections:` — cross-warehouse sources

Named read-only sources (BigQuery or a second Postgres) read from a Postgres/Supabase project via the auto-generated FDW bridge. See [Named Connections](/named-connections). (Not available on a `warehouse: mysql` project.)

---

## Action config blocks

### Common fields (all warehouses)

```sql
config {
  type: "table",                  -- table | view | incremental | assertion | operation | declaration
  schema: "my_schema",           -- overrides defaultDataset / defaultSchema
  database: "my_db",             -- overrides default project / database
  description: "My table.",      -- applied as a table COMMENT (BigQuery/Postgres/MySQL)
  columns: { id: "the id" },     -- per-column COMMENTs
  tags: ["daily", "core"],
  disabled: false,
  hermetic: true,
  dependOnDependencyAssertions: true,
  dependencies: ["other_table"]
}
```

### BigQuery-specific fields

```sql
config {
  type: "table",
  partitionBy: "DATE(created_at)",          -- BigQuery only
  partitionExpirationDays: 90,              -- BigQuery only
  clusterBy: ["customer_id", "region"],     -- BigQuery only
  labels: { team: "analytics" },            -- BigQuery only
  additionalOptions: { kms_key_name: "..." }, -- BigQuery only
  reservation: "projects/.../reservations/my-res" -- BigQuery only
}
```

### PostgreSQL-specific fields

```sql
config {
  type: "table",
  postgres: {
    tablespace: "fast_ssd",
    fillfactor: 80,
    unlogged: false,
    partition: {
      kind: 0,               -- numeric enum: RANGE=0, LIST=1, HASH=2
      columns: ["order_date"]
    },
    indexes: [
      {
        name: "ix_orders_customer",
        columns: ["customer_id"],
        method: 0,           -- numeric enum: BTREE=0, HASH=1, GIN=2, GIST=3, BRIN=4 (omit for btree)
        unique: false,
        where: "",           -- partial index predicate
        include: [],         -- covering index columns
        opclass: ""          -- single operator class applied to every indexed column
      }
    ]
  }
}
```
**Gotcha:** `method`/`kind` are **numeric enums**, not strings — `method: "btree"` fails the config check. Materialized views use `type: "view", materialized: true` with an optional `postgres: { refreshPolicy: "on_dependency_change", noData: true, indexes: [...] }`.

### MySQL / MariaDB-specific fields

```sql
config {
  type: "table",
  mysql: {
    engine: "InnoDB",                 -- ENGINE=
    charset: "utf8mb4",               -- DEFAULT CHARSET=
    collation: "utf8mb4_unicode_ci",  -- COLLATE=
    indexes: [
      { name: "ix_label", columns: ["label"], unique: false }
    ]
  }
}
```
Plain B-tree indexes only (no `where`/`include`/`opclass`/`method` — those are Postgres-only). Partitioning, `FULLTEXT`/`SPATIAL`/prefix indexes, and `row_format` are not in the block yet — use raw DDL in `operations`. `description:`/`columns:` apply as real comments. `type: "view", materialized: true` is emulated as a **refreshed table snapshot** (drop + CTAS each run), honoring `mysql: {}`. MySQL views can't carry comments.

### Supabase-specific fields

```sql
config {
  type: "table",
  supabase: {
    enableRls: true,
    publishToRealtime: true,
    ownerRole: "postgres",
    vectors: [
      {
        column: "embedding",
        dimensions: 1536,
        indexType: "hnsw",
        params: { m: "16", ef_construction: "64" }
      }
    ]
  }
}
```
Supabase extends Postgres, so all `postgres: {}` fields apply too. Dedicated action types: `rlsPolicy`, `realtimePublication`, `wrapper`, `vectorIndex`.

### Export action (`type: "export"`)

Writes a SELECT result to a Parquet/CSV/JSON file. BigQuery compiles to native `EXPORT DATA`
(`gs://` only); Postgres/Supabase export runner-side via DuckDB (`s3://`/`gs://`/local). See the
[File Exports guide](exports.md).

```sql
config {
  type: "export",
  export: {
    location: "s3://bucket/orders/",   -- folder/prefix URI (gs:// | s3:// | local:// | path)
    format: "parquet",                  -- parquet | csv | json
    overwrite: true,                    -- default true
    filename: "orders",                 -- optional; defaults to action name
    options: {}                         -- optional format passthrough
  }
}
SELECT * FROM ${ref("orders")}
```

Object-store credentials live in a `storage` section of the gitignored `.df-credentials.json`
(keyed by scheme — `s3`/`gcs`), used only by the DuckDB path; `local://` needs none and BigQuery
uses its own GCS access.

---

## Cross-warehouse compatibility

| Feature | BigQuery | Postgres | Supabase | MySQL/MariaDB |
|---------|----------|----------|----------|----------------|
| `partitionBy` / `clusterBy` | ✓ | ✗ (use `postgres.partition`) | ✗ | ✗ |
| `postgres.indexes` | ✗ | ✓ | ✓ | ✗ (use `mysql.indexes`) |
| `mysql.indexes` / `mysql.engine` | ✗ | ✗ | ✗ | ✓ |
| `labels` / `reservation` | ✓ | ✗ | ✗ | ✗ |
| `description:` / `columns:` comments | ✓ | ✓ | ✓ | ✓ (tables only — not views) |
| `materialized` view | ✓ (auto-refresh) | ✓ (drop+recreate, opt-in REFRESH) | ✓ (same as Postgres) | ✓ (emulated: refreshed table snapshot) |
| `supabase.enableRls` | ✗ | ✗ | ✓ | ✗ |
| named `connections` (FDW sources) | source only | ✓ (read side) | ✓ (read side) | ✗ |
| `type: "export"` (files) | ✓ (`EXPORT DATA`, gs:// only) | ✓ (DuckDB; s3/gs/local) | ✓ (DuckDB; incl. Supabase Storage) | ✗ |

SQLAnvil emits a **compilation error** if a warehouse-specific config field is used against the wrong warehouse.

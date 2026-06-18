# File Exports (`type: "export"`)

Write the result of a query to a **Parquet / CSV / JSON** file at a cloud or local location.

> **Requires `@sqlanvil/core` 1.8.0 or newer.** Pin it in `workflow_settings.yaml` with
> `sqlanvilCoreVersion: 1.8.0`.

A `type: "export"` action is a **sink**: it runs after the relations its query `${ref()}`s and
produces a file instead of a warehouse object.

```sql
-- definitions/orders_export.sqlx
config {
  type: "export",
  export: {
    location: "s3://my-bucket/orders/",   // folder/prefix URI
    format: "parquet",                     // parquet | csv | json (json = JSONL)
    overwrite: true                        // default true
  }
}
SELECT order_id, total::text AS total FROM ${ref("orders")} ORDER BY order_id
```

## How it runs per warehouse

| Warehouse | Mechanism |
| :--- | :--- |
| **BigQuery** | Compiles to native `EXPORT DATA OPTIONS(uri='gs://…/<name>_*.parquet', …) AS <SELECT>`. In-engine, uses the warehouse's own GCS access. **`gs://` only**. |
| **Postgres / Supabase** | The CLI runs the export via **DuckDB**: it attaches the database read-only and `COPY (SELECT … FROM postgres_query('pg', <your SELECT>)) TO '<uri>'`. The SELECT runs on Postgres; DuckDB only encodes + uploads. Targets `s3://`, `gs://`, Supabase Storage, or a local path. |

MySQL/MariaDB export is not supported yet.

## `export` options

| Field | Description |
| :--- | :--- |
| `location` | **Required.** Destination folder/prefix URI. SQLAnvil derives the filename. `${}` interpolation works (e.g. dates, `schemaSuffix`). |
| `format` | **Required.** `parquet`, `csv`, or `json` (JSONL). |
| `overwrite` | Replace an existing file/object. Default `true`. |
| `filename` | Output base filename. Defaults to the action name. |
| `options` | Format-specific passthrough (e.g. `compression`). |

**BigQuery requires a `*` in the export URI** — SQLAnvil injects it automatically
(`<location>/<name>_*.<ext>`). DuckDB writes a single file (`<location>/<name>.<ext>`).

## Schemes & per-warehouse validity

| Scheme | BigQuery | Postgres / Supabase |
| :--- | :--- | :--- |
| `gs://` | ✓ (native) | ✓ (DuckDB) |
| `s3://` (incl. Supabase Storage S3 endpoint) | ✗ | ✓ |
| `local://…` or a relative/absolute path | ✗ | ✓ (great for debugging) |

A BigQuery project with a non-`gs://` location is a **compile error**.

## Storage credentials

Cloud destinations on Postgres/Supabase need credentials in a new `storage` section of the
gitignored `.df-credentials.json` (BigQuery uses its own GCS access; `local://` needs none):

```jsonc
{
  "host": "…", "port": 5432, "database": "…", "user": "…", "password": "…",
  "storage": {
    "s3": {
      "endpoint": "<project-ref>.supabase.co/storage/v1/s3",   // Supabase Storage (S3-compatible)
      "region": "us-east-1",
      "accessKeyId": "…",
      "secretAccessKey": "…"
    },
    "gcs": { "keyId": "…", "secret": "…" }                     // GCS HMAC keys
  }
}
```

Per-environment via [`--environment`](./environments.md)'s credentials file.

## Notes

- Exports depend on the relations they `${ref()}`, so they run last and `--include-dependencies`
  pulls in upstream models.
- The DuckDB dependency is installed with `@sqlanvil/cli` and loaded only when a Postgres/Supabase
  export runs.
- An empty result still writes a file (header-only for CSV).

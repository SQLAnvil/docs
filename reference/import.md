# Class: Import

Imports load a Parquet/CSV/JSON file into a table in the warehouse — the inverse of
`type: "export"`. The resulting table is `ref()`-able by downstream actions, so an import is a
*producer* (unlike export, a terminal sink). It is config-only: no SQL body — the source is the
file at `location`, the destination is the action's target.

```sql
-- definitions/orders_in.sqlx
config {
  type: "import",
  import: { location: "s3://bucket/orders/*.parquet", format: "parquet", overwrite: true }
}
-- Note: no SQL should be present.
```

On BigQuery this compiles to a native `LOAD DATA` statement (gs:// only). On Postgres/Supabase
the runner performs the load via DuckDB (read the file, write into the warehouse). Data is loaded
as-is (no transform) — shape it in a downstream model.

## Hierarchy

* ActionBuilder‹Import›

  ↳ **Import**

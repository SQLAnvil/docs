# Class: Export

Exports write the result of a SELECT to a Parquet/CSV/JSON file at a cloud or local location.

```sql
-- definitions/dump.sqlx
config {
  type: "export",
  export: { location: "s3://bucket/orders/", format: "parquet", overwrite: true }
}
SELECT * FROM ${ref("orders")}
```

On BigQuery this compiles to a native `EXPORT DATA` statement (gs:// only). On
Postgres/Supabase the runner performs the export via DuckDB.

## Hierarchy

* ActionBuilder‹Export›

  ↳ **Export**

## Index

### Methods

* [query](#query)

## Methods

###  query

▸ **query**(`contextable`: Contextable‹IActionContext, string›): *this*

Sets the SELECT whose result is exported.

**Parameters:**

Name | Type |
------ | ------ |
`contextable` | Contextable‹IActionContext, string› |

**Returns:** *this*

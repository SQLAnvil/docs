# Named Connections: Cross-Warehouse Sources

Read a table that lives in **another warehouse** — BigQuery, or a second Postgres — as if it were
local, with no ETL job. You declare the remote warehouse once as a **named connection**, tag a
declaration with it, and SQLAnvil auto-generates the Foreign Data Wrapper (FDW) bridge so
`${ref(...)}` resolves to a live foreign table.

> **Requires `@sqlanvil/core` 1.2.0 or newer** for Postgres/Supabase sources (the
> `postgres_fdw` user mapping + run-time credential injection landed in 1.2.0). BigQuery
> sources work from 1.1.1. Pin it in `workflow_settings.yaml` with `sqlanvilCoreVersion: 1.2.0`.

> **One write warehouse, many read-only sources.** SQLAnvil writes to exactly one warehouse —
> your `warehouse:`. Named connections are read-only sources you pull *from*; SQLAnvil never
> writes back to them. The read (write) warehouse must be **`postgres`** or **`supabase`**,
> because the bridge is a Postgres FDW. On Supabase, enable the **`wrappers`** extension
> (Dashboard → Database → Extensions).

## 1. Declare the connection

In `workflow_settings.yaml`, add a `connections:` map. Each entry names a warehouse and how to
reach it. Example — a read-only BigQuery public dataset, read from a Supabase project:

```yaml
warehouse: supabase          # the one warehouse SQLAnvil writes to
defaultDataset: public
sqlanvilCoreVersion: 1.2.0

connections:
  bigquery_public:
    platform: bigquery
    project: bigquery-public-data
    dataset: geo_us_boundaries
    saKeyId: "REPLACE_WITH_VAULT_SECRET_ID"   # non-secret Vault pointer (see step 4)
```

Connection fields by platform:

| Platform | Fields |
| :--- | :--- |
| `bigquery` | `platform`, `project`, `dataset`, `saKeyId` |
| `postgres` / `supabase` | `platform`, `host`, `port`, `database`, `defaultSchema` |

For a **`postgres`/`supabase` source**, the secret `user`/`password` are **not** in
`workflow_settings.yaml` — they go in the gitignored `.df-credentials.json` under
`connections.<name>` and are injected into the generated `CREATE USER MAPPING` at run time (see
[step 5](#5-optional-generate-the-declaration-with-introspect) for the file shape). The
non-secret `host`/`port`/`database` above feed the foreign server at compile time.

Nothing secret lives in `workflow_settings.yaml`: `saKeyId` is the **id** of a secret stored in
Supabase Vault (step 4), not the credential itself.

## 2. Add a connection-tagged declaration

A declaration tagged with `connection:` tells SQLAnvil "this source lives in another warehouse —
build the bridge to it." It needs `columnTypes` (the FDW must know the column types to create the
foreign table), expressed as **Postgres** types since the foreign table is a Postgres object:

```js
// definitions/sources/zip_codes.sqlx
config {
  type: "declaration",
  connection: "bigquery_public",
  name: "zip_codes",
  columnTypes: {
    zip_code: "text",
    internal_point_lat: "float8",
    internal_point_lon: "float8"
  }
}
```

You can write `columnTypes` by hand, or generate the whole declaration from the live source
schema with `sqlanvil introspect` (see [step 5](#5-optional-generate-the-declaration-with-introspect)).

## 3. Reference it like any other source

Downstream models `${ref(...)}` the declaration by name — no special syntax:

```sql
-- definitions/staging/stg_zip_codes.sqlx
config { type: "view", schema: "staging" }
SELECT
  zip_code,
  internal_point_lat AS lat,
  internal_point_lon AS lon
FROM ${ref("zip_codes")}
```

## 4. Store the source credential in Vault (BigQuery)

The FDW server reads the BigQuery service-account key at run time from Supabase Vault, by **id**.
Create the secret once in the Supabase SQL editor:

```sql
-- Paste your service-account JSON. Returns the secret id.
select vault.create_secret('<paste service-account JSON>', 'bigquery_sa');

-- Read the id back:
select id from vault.secrets where name = 'bigquery_sa';
```

Put the returned id in `connections.bigquery_public.saKeyId`.

## 5. (Optional) Generate the declaration with `introspect`

Instead of hand-writing `columnTypes`, let SQLAnvil read the remote schema and write the
declaration for you. `introspect` connects to the source from your machine at build time, so it
needs **read credentials for the source connection**. Put them in `.df-credentials.json` under a
`connections` map (keyed by connection name) — alongside, not mixed into, your flat write-warehouse
credentials:

```json
{
  "host": "aws-1-us-east-1.pooler.supabase.com",
  "port": 5432,
  "database": "postgres",
  "user": "postgres.<project-ref>",
  "password": "<warehouse-password>",
  "sslMode": "require",
  "defaultSchema": "public",

  "connections": {
    "bigquery_public": { "credentials": { "type": "service_account", "...": "service-account JSON" } }
  }
}
```

- **BigQuery source:** `{ "credentials": <service-account key JSON> }`.
- **Postgres/Supabase source:** `{ "host", "port", "database", "user", "password", "sslMode" }`.

This file stays gitignored. The `connections` map is read only by `introspect`; `run` reads the
flat warehouse credentials and ignores it. (This is distinct from the run-time Vault `saKeyId` the
FDW server uses inside Supabase — that's the *runtime* credential; this is the *build-time* one for
reading the schema.)

Then generate the declaration:

```bash
sqlanvil introspect bigquery_public geo_us_boundaries.zip_codes \
  --output definitions/sources/zip_codes.sqlx
```

`introspect <connection> <schema.table>` maps each source column to a Postgres type. Without
`--output` it prints to stdout.

## 6. Compile

```bash
sqlanvil compile .
```

Compiling generates the bridge automatically. For the `bigquery_public` connection you'll see
these extra actions alongside your own models:

- **`bigquery_public_srv`** — an operation that enables the wrapper extension and creates the
  foreign server (named `<connection>_srv`).
- **`bigquery_public_ext.zip_codes`** — the ref-able foreign table, in the auto-named
  `<connection>_ext` schema.

Multiple declarations on the same connection share one server.

## 7. Run

```bash
sqlanvil run .
```

This creates the server and foreign table, then builds everything downstream — one pass, no
separate sync job.

## How the bridge maps

| You write | SQLAnvil generates |
| :--- | :--- |
| connection `bigquery_public` | server `bigquery_public_srv` + schema `bigquery_public_ext` |
| declaration `zip_codes` | foreign table `bigquery_public_ext.zip_codes` |
| `${ref("zip_codes")}` | a query against the live foreign table |

## Worked example

[`examples/supabase_bigquery_mailing_list`](https://github.com/sqlanvil/sqlanvil/tree/main/examples/supabase_bigquery_mailing_list)
is a complete, runnable version of this pattern: it joins Supabase commerce data with BigQuery
geo data (via a named connection) to build a proximity mailing list. Copy it out and follow its
README to run end-to-end.

## Troubleshooting

| Symptom | Likely cause | Fix |
| :--- | :--- | :--- |
| `Unknown connection "X" on declaration "Y"` at compile | the declaration's `connection:` doesn't match a key under `connections:` | Check the name. If it *does* match, ensure `@sqlanvil/core` is **≥ 1.1.1** — 1.1.0 dropped connections in the published package. |
| `Declaration "X" on connection "Y" requires columnTypes` | a connection-tagged declaration with no `columnTypes` | Add them, or run `sqlanvil introspect <conn> <schema.table> --output <file>`. |
| `Reading connection "X" from a bigquery warehouse is not yet supported` | your `warehouse:` is `bigquery` | The read side must be `postgres`/`supabase` — the FDW bridge is a Postgres feature. |
| Wrapper/extension errors on `run` | the `wrappers` extension isn't enabled on the database | Enable it (Supabase Dashboard → Database → Extensions). |

## Related

- [Getting Started with Supabase](./getting-started-supabase.md) — set up the write warehouse first.
- [Hybrid Warehouse Architecture](./hybrid_warehouses_supabase_bigquery.md) — the architectural patterns behind cross-warehouse stacks.

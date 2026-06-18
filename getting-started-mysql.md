# Getting Started with MySQL / MariaDB

Connect a SQLAnvil project to MySQL or MariaDB and build your first table.

> One `mysql` adapter targets **both MySQL 8 and MariaDB 11** — the same project runs against either.
> SQLAnvil generates portable MySQL-dialect SQL; engine-specific features ride through `operations`.

## 1. Install the CLI

```bash
npm i -g @sqlanvil/cli
sqlanvil --version        # sqlanvil 1.7.0 (Dataform core 3.0.59)
```

## 2. Scaffold a project

```bash
sqlanvil init my_project --warehouse mysql
```

This creates:

```
my_project/
  workflow_settings.yaml      # warehouse: mysql + defaults (committed)
  .df-credentials.json        # your connection (gitignored — holds the password)
  .gitignore
  definitions/                # your models go here
```

`workflow_settings.yaml` holds non-secret settings; **`.df-credentials.json` holds the connection
and is gitignored** so your password never lands in version control. `workflow_settings.yaml` looks like:

```yaml
warehouse: mysql
defaultDataset: sqlanvil           # the MySQL DATABASE (MySQL has no schema-vs-database split)
defaultAssertionDataset: sqlanvil_assertions
```

## 3. Boot a local database (optional)

To try it locally:

```bash
docker run --rm --name mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=sqlanvil -p 3306:3306 -d mysql:8
# or MariaDB:
docker run --rm --name mariadb -e MARIADB_ROOT_PASSWORD=password -e MARIADB_DATABASE=sqlanvil -p 3307:3306 -d mariadb:11
```

## 4. Fill in `.df-credentials.json`

Strict JSON — **no comments, no trailing commas.** The MySQL connection shape (note: **no
`defaultSchema`** — in MySQL the "schema" *is* the database):

```json
{
  "host": "localhost",
  "port": 3306,
  "database": "sqlanvil",
  "user": "root",
  "password": "password",
  "sslMode": "disable"
}
```

`sslMode` is `"disable"` (local) or `"require"` (managed/TLS). Default port is `3306` (use `3307` for
the MariaDB container above).

## 5. Add a model

```bash
cat > my_project/definitions/hello.sqlx <<'EOF'
config { type: "table" }
SELECT 1 AS id, 'hello sqlanvil' AS msg
EOF
```

## 6. Compile and run

```bash
# Compile checks your project (no database needed):
sqlanvil compile my_project
# → Compiled 1 action(s).  sqlanvil.hello [table]

# Run applies it to MySQL:
sqlanvil run my_project --credentials my_project/.df-credentials.json
```

`sqlanvil run` creates the database (`CREATE DATABASE IF NOT EXISTS`) and builds the table.

## 7. Verify

```sql
SELECT * FROM `sqlanvil`.`hello`;
```

Identifiers compile to two-part backticks — `` `database`.`table` ``.

## MySQL / MariaDB features

- **`mysql: {}` block** — declare secondary indexes and table options in a model's config:
  ```sql
  config {
    type: "table",
    mysql: {
      engine: "InnoDB",
      charset: "utf8mb4",
      indexes: [{ name: "ix_label", columns: ["msg"], unique: false }]
    }
  }
  ```
  Plain B-tree indexes only (no partial/covering/opclass — those are Postgres-only).
- **Incremental tables** — `config { type: "incremental", uniqueKey: ["id"] }` compiles to
  `INSERT … ON DUPLICATE KEY UPDATE`; the adapter auto-creates the matching unique index, so you
  don't add your own.
- **Comments** — `description:` and `columns: { col: "…" }` apply as real table/column comments
  (read back from `information_schema`). Tables/incrementals only — MySQL views can't carry comments.
- **Materialized views** — `config { type: "view", materialized: true }` is emulated as a **refreshed
  table snapshot** (rebuilt each run), honoring the `mysql: {}` block. (MySQL has no native matviews.)
- **Procedures / functions / triggers** — use `type: "operations"`. Statements are separated by
  `---`, never `;` — so don't use `DELIMITER`; a `CREATE PROCEDURE … BEGIN … END` body is one
  statement and its internal `;` survive.

## Notes & limits

- Backtick-quote identifiers in any raw DDL (`` `col` ``), never double-quote.
- Cross-warehouse named `connections` (the FDW bridge) are **not** available on MySQL — and MySQL
  can't be a source for one. No `sqlanvil introspect` for MySQL sources.
- Not in the `mysql: {}` block yet: partitioning, `FULLTEXT`/`SPATIAL`/prefix indexes, `row_format`
  — express those with raw DDL in `operations`.

## Troubleshooting

| Symptom | Likely cause | Fix |
| :--- | :--- | :--- |
| `Could not connect to MySQL … Access denied` | Wrong user/password | Check `.df-credentials.json`; for the local container the user is `root`, password `password`. |
| `Could not connect … ECONNREFUSED` | Wrong host/port, or DB not up yet | Confirm the container is running and the port (3306 MySQL, 3307 MariaDB); wait for it to accept connections. |
| `Unexpected property "defaultSchema"` (or other) | Postgres-shaped creds on a mysql project | MySQL `.df-credentials.json` has no `defaultSchema` — use `host/port/database/user/password/sslMode`. |
| `Unsupported warehouse "mysql"` | `sqlanvilCoreVersion` pinned below 1.5.0 | MySQL landed in core **1.5.0**; remove the pin or set it ≥ 1.5.0. |
| TLS / `HANDSHAKE_NO_SSL_SUPPORT` | `sslMode: require` against a server without TLS | Use `"sslMode": "disable"` for local/non-TLS servers. |

## Next steps

- **Using Claude Code?** Install the `sqlanvil-toolkit` plugin so the assistant writes correct
  SQLAnvil code — see [Using SQLAnvil with Claude Code](./claude-code.md).
- Isolate dev/staging/prod with [named environments](./environments.md) (`--environment`), or use
  `--schema-suffix dev` to build into a `<database>_dev` sandbox.
- See the [Configs Reference](./configs-reference.md) for the full `mysql: {}` and action-config surface.

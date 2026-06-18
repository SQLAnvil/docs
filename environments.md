# Named Environments

Run the same project against dev, staging, and prod without juggling flags. You define each
**environment** once in `workflow_settings.yaml` — its schema suffix, vars, and **its own
credentials file** — and select it with `--environment <name>` on `compile`, `run`, or `test`.

> **Requires `@sqlanvil/core` 1.7.0 or newer.** Pin it in `workflow_settings.yaml` with
> `sqlanvilCoreVersion: 1.7.0`.

> **Why not just `--schema-suffix`?** A schema suffix only renames output schemas — it can't point
> at a different **database or credentials**. Prod and staging usually differ by more than a name.
> A named environment bundles the suffix *and* the connection; `--schema-suffix` stays the
> low-level primitive underneath.

## Define environments

Add an `environments:` map to `workflow_settings.yaml`. It holds only **non-secret** overrides plus
a **pointer** to each environment's credentials file — never the secrets themselves.

```yaml
warehouse: postgres
defaultDataset: analytics

environments:
  dev:
    schemaSuffix: dev                      # output schemas get a _dev suffix
    credentials: .df-credentials.dev.json
  prod:
    defaultDatabase: prod_db
    vars:
      region: us-prod
    credentials: .df-credentials.prod.json
```

Per-environment fields (all optional):

| Field | Effect |
| :--- | :--- |
| `schemaSuffix` | Appends `_<suffix>` to every output schema (same as `--schema-suffix`). |
| `vars` | Variables for `${sqlanvil.projectConfig.vars.…}` (merged per-key). |
| `defaultDatabase` | Overrides the default database / BigQuery project. |
| `defaultLocation` | Overrides the default location (BigQuery). |
| `credentials` | Path (relative to the project dir) to this environment's credentials file. |

## Credentials stay gitignored

Each environment's `credentials:` is just a **path** — the actual host/password lives in that file,
which is gitignored. `sqlanvil init` ignores `.df-credentials*.json`, so `.df-credentials.dev.json`,
`.df-credentials.prod.json`, etc. are all kept out of version control. Nothing secret goes in the
committed `workflow_settings.yaml`.

## Use an environment

```bash
sqlanvil run . --environment prod
# loads prod's overrides (defaultDatabase, vars) AND its credentials file
```

`--environment` works on `compile`, `run`, and `test`. `compile` applies the config overrides
(schema suffix, vars, …); `run`/`test` additionally connect with the environment's credentials.

## Precedence

**Explicit CLI flag > environment > `workflow_settings` defaults.** An explicit flag always wins, so
you can override one piece of an environment ad hoc:

```bash
sqlanvil run . --environment dev --schema-suffix qa
# dev's credentials + vars, but the schema suffix is qa (the flag overrides the env)
```

- `vars` merge **per-key** — a `--vars` key overrides that key from the environment, which overrides
  that key from `workflow_settings`; other keys are preserved.
- Credentials: an explicit `--credentials <path>` overrides the environment's `credentials`; with no
  flag, the environment's file is used; with neither, the default `.df-credentials.json`.

## Errors

- `--environment <name>` with no matching entry fails fast: `Environment "<name>" not found.
  Available environments: …`.
- `--environment` with no `environments:` block reports `No environments defined in
  workflow_settings.yaml`.

## Notes

- Per-environment **warehouse type** is not supported — `warehouse:` stays global (prod/staging
  usually differ by database/credentials, not engine). Per-env `defaultDatabase` + `credentials`
  cover that.
- Declared sources are exempt from `schemaSuffix` (as always), so a `dev` run reads your real
  sources while writing to `_dev` schemas.

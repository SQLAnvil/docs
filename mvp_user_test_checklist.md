# SQLAnvil MVP — Real-User Acceptance Test Checklist

A by-hand checklist to answer one question: **can a typical user — a data engineer who is NOT a
sqlanvil contributor — install, configure, author, and run a sqlanvil project on Postgres/Supabase,
with clear errors and docs?**

This is deliberately *not* the automated test suite. Run it yourself, as a new user would, on real
infrastructure. For each item: do the action, confirm the **pass criteria**, record the result.

## Results log

**2026-06-04 — Linux (Docker `node:20`) + published `@sqlanvil/cli@1.0.0` + real Supabase: PASS.**
Validated as a real user, no repo checkout:
- Install `npm i -g @sqlanvil/cli`; `sqlanvil --version` → `sqlanvil 1.0.0 (Dataform core 3.0.59)`
  (global `bin` works, no `npx` needed).
- `sqlanvil init my_proj --warehouse supabase` → correct scaffold (warehouse, creds template,
  gitignore, definitions tree).
- `compile` on a fresh project fetched `@sqlanvil/core@1.0.0` from npm and compiled — the
  pre-publish `sqlanvilCoreVersion` dead-end is gone.
- `run` against a real Supabase project (session pooler, IPv4) created **table, incremental (+PK),
  view, materialized view, operation/function, and an assertion** — all succeeded.
- **Idempotency:** identical second `run` — no duplicate-PK error, matview refreshed in place —
  confirms the `dataformVersion` capability gate works in the shipped build.
- **Error clarity:** wrong password → `password authentication failed for user "postgres"` (clear).
- Finding (folded into `getting-started-supabase.md`): the Supabase **pooler host must be copied
  verbatim** — the `aws-0-`/`aws-1-` prefix + region slug aren't guessable; a constructed host fails
  with `tenant ... not found`.

**Still to validate:** Windows (clean Win11), plain `postgres` (non-Supabase) path, BigQuery path.

## Test environments to have ready

- A **clean machine** (or container) with no sqlanvil checkout — for the install test.
- A **local Postgres** you control (e.g. `./tools/postgres/run-postgres-db.sh`, or any local PG).
- A **real cloud Postgres** (managed PG) — optional but recommended.
- A **real Supabase project** (free tier is fine) — to test the Supabase path for real.
- Node.js installed.

---

## Section 0 — Release prerequisites (GATING)

> If any of these fail, a typical user is blocked before they start. Stop and fix before release.

- [x] **CLI installs without building from source.** `@sqlanvil/cli@1.0.0` + `@sqlanvil/core@1.0.0`
      published to npm 2026-06-04; `npm i -g @sqlanvil/cli` works on a clean machine.
- [x] **The `init` → `compile` → `run` golden path works on a freshly-init'd project with zero manual
      `node_modules` surgery.** Validated 2026-06-04 (see Results log): `compile` fetches
      `@sqlanvil/core@1.0.0` from npm and runs end-to-end against real Supabase.
- [x] **A quickstart doc exists** matching the published reality —
      `getting-started-supabase.md` (install → init → connect → first table).

---

## Section 1 — Install & first contact (clean machine)

- [ ] Install per the published quickstart; `sqlanvil --version` and `sqlanvil help` work.
- [ ] `sqlanvil help init` / `help run` / `help compile` are accurate and readable.
- [ ] First command run prints something sensible (not a stack trace) when given no/early args.

## Section 2 — Create a project (`init`)

- [ ] `sqlanvil init my_proj --warehouse postgres` creates: `workflow_settings.yaml`
      (`warehouse: postgres`, `defaultDataset`, `defaultAssertionDataset`), a `.df-credentials.json`
      template, a `.gitignore` that excludes the credentials file, and a `definitions/` structure.
- [ ] Generated files are readable and obviously editable — a user understands what to fill in.
- [ ] `sqlanvil init my_proj --warehouse supabase` → credentials template uses `sslMode: require`
      and a `db.<project-ref>.supabase.co` host placeholder.
- [ ] BigQuery (default) still requires a project + location; omitting them gives a clear error.
- [ ] **Arg order:** confirm whether `sqlanvil init my_proj --warehouse postgres` works *and*
      `sqlanvil init --warehouse postgres my_proj` works. (Known rough edge — option-before-positional
      currently errors. A typical user will try both.)

## Section 3 — Connect to a real database

- [ ] Fill `.df-credentials.json` for your **real Postgres**; the connection test succeeds.
- [ ] **Supabase:** point at a real project — `db.<ref>.supabase.co`, port `5432` (also try `6543`
      pooler), user `postgres`, `sslMode: require`. It connects.
- [ ] Wrong password / unreachable host / bad sslMode → a **clear, actionable error**, not a raw
      stack trace.
- [ ] Editing `.df-credentials.json` with a stray comment or trailing comma fails with a readable
      message (the parser is strict — no comment keys).

## Section 4 — Author & run the core model types (against your real DB)

- [ ] **Declaration** for an existing source table; `${ref("source")}` resolves and shows in the DAG.
- [ ] **Table** (from a source or `VALUES`); `run`; verify the rows land in `psql` / the Supabase
      table editor.
- [ ] **View**, and a **materialized view** (`type:"view", materialized:true`) — confirm it's a real
      matview (`\dm` / `pg_matviews`), and that re-run refreshes it.
- [ ] **Incremental** with `uniqueKey`; `run` twice — the second run appends/upserts without error;
      a PK added via `when(!incremental())` does not re-run.
- [ ] `postgres: { fillfactor, indexes: [...] }` — verify the index and storage option exist (`\d+`).
- [ ] `description` + `columns: {}` — verify `COMMENT`s show in `\d+` / the dashboard.
- [ ] **Assertions** (`uniqueKey` / `nonNull` / `rowConditions`): a clean dataset passes; a
      deliberately-bad one **fails clearly**, naming the offending assertion.
- [ ] **Operation** creating a function/procedure with a `$$ ... $$` body and `---` separators —
      runs intact; `CALL`/`SELECT fn()` works.
- [ ] `compile` output (and `--json`) is readable and reflects the dependency graph.

## Section 5 — Real workflows & failure modes

- [ ] **Subsetting:** `--actions <name>`, `--include-deps`, `--tags <tag>` run the expected subset.
- [ ] `--full-refresh` rebuilds an incremental from scratch.
- [ ] `--schema-suffix dev` writes to a suffixed schema; declarations are **not** suffixed (sources
      still read from their real location) — confirm a dev run reads prod sources, writes dev output.
- [ ] A **SQL syntax error** surfaces clearly **at run time** (reminder: there is no Postgres
      `--dry-run`/validate yet — errors only appear on `run`). Is the message good enough?
- [ ] Editing a model and re-running picks up the change.
- [ ] A mid-run failure (e.g. one model errors) leaves a sane, understandable state.

## Section 6 — Docs & discoverability

- [ ] A new user can follow the quickstart start-to-finish **without insider knowledge**.
- [ ] Docs/README match actual CLI flags and output.
- [ ] Error messages point to a next step (fix creds, run `help`, etc.).

## Section 7 — Supabase-specific (if targeting Supabase)

- [ ] `rlsPolicy` action — create a policy and confirm it **enforces** (rows filtered for the
      `authenticated` role).
- [ ] `realtimePublication`, `vectorIndex`, `wrapper` as your use case needs.
- [ ] Results are visible/queryable from the Supabase client SDK as expected.

---

## Known rough edges to verify (found while building/testing)

These are the things most likely to trip a real user — confirm each is acceptable or fix before release:

1. **Publishing not done** → install and the `init → compile → run` golden path are blocked
   (Section 0). This is the single biggest gap between "works in-repo" and "a user can use it."
2. **`init` arg order** — the positional project dir must come *before* `--warehouse`; option-first
   currently errors (`Arg neither flag name nor flag value`). Likely worth fixing — users won't guess.
3. **`.df-credentials.json` is strict JSON** — comment keys / trailing commas are rejected. Templates
   and docs must be comment-free, and the error should be readable.
4. **No Postgres `--dry-run` / `validate`** — SQL errors only surface at `run` time. Make sure runtime
   error messages are good (this is the nice-to-have `sqlanvil validate` item).
5. **`.df-credentials.json` keeps the legacy `df` name** — cosmetic, but a user may find it odd in a
   tool called sqlanvil.

## Sign-off

MVP is **real-user-ready** when Section 0 passes and Sections 1–6 pass on **at least one real
Postgres and one real Supabase project**, with no step requiring insider/contributor knowledge.

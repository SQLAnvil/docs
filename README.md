# SQLAnvil Docs

Reference docs and design documents for [SQLAnvil](https://github.com/sqlanvil/sqlanvil) — the SQL workflow tool for BigQuery, PostgreSQL, and Supabase.

The published docs site is the best place to read these: **[sqlanvil.com/docs](https://sqlanvil.com/docs/)**.

## Links

- **Code** — [github.com/sqlanvil/sqlanvil](https://github.com/sqlanvil/sqlanvil)
- **Website** — [sqlanvil.com](https://sqlanvil.com)
- **Docs** — [sqlanvil.com/docs](https://sqlanvil.com/docs/)

## In this repo

- `getting-started-supabase.md` — connect a SQLAnvil project to Supabase (install → init → connection details → first table)
- `named-connections.md` — read a BigQuery table as a live foreign table via named connections — the auto-generated FDW bridge
- `claude-code.md` — use SQLAnvil with Claude Code: install the `sqlanvil-toolkit` plugin (the `sqlanvil-engineering-fundamentals` skill + slash commands)
- `reference/` — generated API reference (regenerated from the code via `scripts/regenerate_docs` in the main repo)
- `configs-reference.md`, `packages.md` — configuration and package reference
- `npm_publishing.md`, `upstream_merge_guide.md` — release & upstream-sync runbooks
- `gcp_test_project_setup.md` — runbook for setting up a GCP project to run the BigQuery integration tests
- `mvp_user_test_checklist.md` — by-hand real-user acceptance checklist (can a typical user install, configure, author, and run a project?)
- `v2_roadmap.md` — post-V1 feature backlog (more databases, file sources, Dataform→SQLAnvil conversion, interactive `init`)
- Design documents — `postgres_first_class_design.md`, `postgres_reintegration_assessment.md`, `hybrid_warehouses_supabase_bigquery.md`, and others

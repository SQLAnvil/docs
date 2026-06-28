# SQLAnvil — Design & Contributor Docs

Internal **design specs, contributor guides, and release runbooks** for
[SQLAnvil](https://github.com/sqlanvil/sqlanvil) — the SQL workflow tool for BigQuery, PostgreSQL,
Supabase, and MySQL/MariaDB.

> **Looking for user docs?** They've moved. **User-facing documentation now lives in one place:
> [sqlanvil.com/docs](https://sqlanvil.com/docs/)** (source in the `sqlanvil-com` repo). Getting-started
> guides, the per-warehouse guides, packages, named connections, and the generated reference are all
> there. This repo no longer holds user docs — keeping them in one canonical home avoids drift.

## Links

- **Code** — [github.com/sqlanvil/sqlanvil](https://github.com/sqlanvil/sqlanvil)
- **User docs** — [sqlanvil.com/docs](https://sqlanvil.com/docs/) (source: `sqlanvil-com`)
- **Website** — [sqlanvil.com](https://sqlanvil.com)

## In this repo (design & contributor docs only)

- `postgres_first_class_design.md` — design spec: Postgres as a first-class (non-BigQuery) adapter
- `postgres_reintegration_assessment.md` — technical assessment for the Postgres adapter reintegration (archival)
- `hybrid_warehouses_supabase_bigquery.md` — design doc: hybrid Supabase + BigQuery workflows
- `v2_roadmap.md` — post-V1 feature backlog
- `upstream_merge_guide.md` — runbook: merging upstream Dataform changes into the fork
- `npm_publishing.md` — runbook: the npm publish/release process
- `gcp_test_project_setup.md` — runbook: setting up a GCP project for the BigQuery integration tests
- `mvp_user_test_checklist.md` — by-hand real-user acceptance checklist
- `rename_checklist.md`, `rename_handoff.md` — artifacts from the Dataform → SQLAnvil rename
- `claude-code.md` — using SQLAnvil with Claude Code (AI-agent authoring guidance)

> Generated API/config reference is produced by `scripts/regenerate_docs` in the main repo and now
> publishes **directly into `sqlanvil-com`** (no longer mirrored here).

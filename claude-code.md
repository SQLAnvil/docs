# Using SQLAnvil with Claude Code

SQLAnvil ships a **Claude Code skill** that teaches the assistant to write correct SQLAnvil
code. Because SQLAnvil is a fork of Dataform, an AI's Dataform/BigQuery instincts produce
subtly broken SQLAnvil projects — wrong config blocks, wrong credentials shape, BigQuery DDL,
`;` separators, the wrong CLI. The **sqlanvil-engineering-fundamentals** skill is the correction
layer, and it also teaches the SQLAnvil-specific features (the `postgres: {}` block, named
connections, `introspect`).

## Install

The skill ships in the **`sqlanvil-toolkit`** plugin on the public `ihistand` marketplace. In a
Claude Code session:

```bash
# 1. Add the marketplace (once)
/plugin marketplace add ihistand/claude-plugins

# 2. Install the toolkit
/plugin install sqlanvil-toolkit@ihistand
```

Then restart Claude Code. (The marketplace is a public GitHub repo, so `/plugin marketplace add`
works directly — no extra setup.)

## What you get

**Skill — `sqlanvil-engineering-fundamentals`.** Activates automatically when you edit a `.sqlx`
file, `workflow_settings.yaml`, or `.df-credentials.json` in a Postgres/Supabase project. It
steers Claude onto:

- Flat `warehouse:` config and a flat `PostgresConnection` `.df-credentials.json` (not the
  BigQuery shapes), and `sqlanvilCoreVersion` (not `dataformCoreVersion`)
- First-class `postgres: {}` DDL — indexes (numeric `method` enum), partitioning, storage
  options, materialized views — never hand-rolled `post_operations` DDL
- `---` as the statement separator; procedures/functions/triggers via `type: "operations"`
- Supabase extras: RLS policies, Realtime, pgvector
- **Named connections** — read a BigQuery table as a live foreign table via the auto-generated
  FDW bridge, generated with `introspect` (see [Named Connections](./named-connections.md))

**Slash commands:**

| Command | Purpose |
| :--- | :--- |
| `/sqlanvil-compile` | Compile and surface config/graph errors (static, no warehouse) |
| `/sqlanvil-test` | Validate model(s) against a `--schema-suffix dev` sandbox |
| `/sqlanvil-run` | Run/deploy to the warehouse with pre-flight checks |
| `/sqlanvil-new-table` | Create a new table via TDD (RED → GREEN → REFACTOR) |
| `/sqlanvil-introspect` | Generate a cross-warehouse source declaration from a named connection |

## Verify it's active

Open a SQLAnvil project and ask Claude to create a table. With the skill active it reaches for a
`postgres: {}` block and `${ref()}`, documents columns, and avoids BigQuery-isms like
`partitionBy` or `bigquery: {}`. You can also run a command directly, e.g. `/sqlanvil-new-table`.

## Skill only (without the plugin)

If you'd rather use just the skill — for a different agent, or a personal-skill setup — the
canonical `SKILL.md` lives in the [`ihistand/claude-skills`](https://github.com/ihistand/claude-skills)
repo under `sqlanvil-engineering-fundamentals/`. Copy it to
`~/.claude/skills/sqlanvil-engineering-fundamentals/SKILL.md` (a personal skill loads in every
project), or symlink it so it stays current as the skill is updated.

## Build your own for your team's standards

Skills layer. You can write a thin, org-specific skill on top of
`sqlanvil-engineering-fundamentals` — name it as a prerequisite and add only your team's
conventions (naming, target datasets, review rules) without repeating the generic rules. The
`acuantia-dataform` plugin in the same marketplace is a worked example of this pattern for a
Dataform team; the same approach works on top of the SQLAnvil skill.

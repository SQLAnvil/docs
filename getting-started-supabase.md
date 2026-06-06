# Getting Started with Supabase

Connect a SQLAnvil project to a Supabase Postgres database and build your first table.

> SQLAnvil targets Supabase as **first-class Postgres** — your models are idiomatic Postgres, plus
> Supabase-specific actions (RLS policies, Realtime, pgvector) when you need them.

## 1. Install the CLI

```bash
npm i -g @sqlanvil/cli
sqlanvil --version        # sqlanvil 1.0.0 (Dataform core 3.0.59)
```

## 2. Scaffold a project

```bash
sqlanvil init my_project --warehouse supabase
```

This creates:

```
my_project/
  workflow_settings.yaml      # warehouse: supabase + defaults (committed)
  .df-credentials.json        # your connection (gitignored — holds the password)
  .gitignore
  definitions/                # your models go here
```

`workflow_settings.yaml` holds non-secret settings; **`.df-credentials.json` holds the connection
and is gitignored** so your password never lands in version control.

## 3. Get your Supabase connection details

In the Supabase dashboard, open your project and click **Connect** (top bar). You'll see several
connection options. **Which one you use matters:**

| Option | Host / Port | User | Use when |
| :--- | :--- | :--- | :--- |
| **Direct connection** | `db.<ref>.supabase.co` : `5432` | `postgres` | Your network has **IPv6** (Supabase direct connections are IPv6-only unless you've bought the IPv4 add-on). |
| **Session pooler** | `aws-<n>-<region>.pooler.supabase.com` : `5432` | `postgres.<ref>` | **IPv4** networks (most laptops, most Docker containers, most CI). Recommended default. |
| Transaction pooler | `aws-<n>-<region>.pooler.supabase.com` : `6543` | `postgres.<ref>` | Serverless/edge only. **Not recommended for SQLAnvil** — transaction mode doesn't support all session/DDL features. |

**Rule of thumb:** if you're not sure your network has IPv6 (you probably don't), use the **Session
pooler** (port 5432). SQLAnvil runs schema DDL, so prefer **session** mode over transaction mode.

Two things the dashboard gives you that you'll need:
- The exact **host / port / user** — **copy them verbatim from the Connect dialog; don't build the
  host by hand.** The pooler host prefix (`aws-0-` vs `aws-1-` — new projects are usually `aws-1-`)
  and the region slug vary per project; a constructed host connects to the *wrong* regional pooler
  and fails with `tenant ... not found`. The pooler `user` is `postgres.<your-project-ref>` (not
  just `postgres`).
- Your **database password** — set when you created the project (Settings → Database → reset if
  you've lost it). The Supabase CLI does **not** expose this.

## 4. Fill in `.df-credentials.json`

It's strict JSON — **no comments, no trailing commas.** Session-pooler example (IPv4):

```json
{
  "host": "aws-0-us-east-1.pooler.supabase.com",
  "port": 5432,
  "database": "postgres",
  "user": "postgres.abcdefghijklmnop",
  "password": "your-database-password",
  "sslMode": "require",
  "defaultSchema": "public"
}
```

Direct-connection example (IPv6):

```json
{
  "host": "db.abcdefghijklmnop.supabase.co",
  "port": 5432,
  "database": "postgres",
  "user": "postgres",
  "password": "your-database-password",
  "sslMode": "require",
  "defaultSchema": "public"
}
```

- `sslMode` must be **`require`** for Supabase.
- `defaultSchema` is the schema your models are created in (`public`, or a dedicated schema like
  `analytics`).
- You can keep credentials elsewhere and pass `--credentials <path>` at run time.

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

# Run applies it to Supabase:
sqlanvil run my_project --credentials my_project/.df-credentials.json
```

## 7. Verify

In the Supabase dashboard → **Table Editor**, switch to the `sqlanvil` schema (or your
`defaultDataset`) and you'll see the `hello` table. Or via SQL:

```sql
select * from sqlanvil.hello;
```

## Troubleshooting

| Symptom | Likely cause | Fix |
| :--- | :--- | :--- |
| Connection times out / `ENETUNREACH` / can't reach host | Using the **direct** host on an **IPv4** network (it's IPv6-only) | Switch to the **Session pooler** (port 5432, host `aws-0-<region>.pooler.supabase.com`, user `postgres.<ref>`). |
| `tenant ... not found` / `Tenant or user not found` | Wrong pooler **host** (you reached a different region's pooler), or a non-qualified username | Copy the **host verbatim** from the Connect dialog (the `aws-0-`/`aws-1-` prefix + region slug aren't guessable), and set `user` to `postgres.<your-project-ref>`. |
| `no pg_hba.conf entry ... no encryption` / SSL errors | SSL not requested | Set `"sslMode": "require"`. |
| `password authentication failed` | Wrong DB password | Reset it in Settings → Database; the CLI can't supply it. |
| `ECIRCUITBREAKER` / "too many authentication failures, new connections are temporarily blocked" | Supabase's pooler temporarily locked the tenant after repeated auth failures — usually a single wrong-password run (the CLI opens several connections at once, so one bad attempt counts as several) | Fix the password, **wait ~1–2 minutes** for the breaker to reset, then retry. Not a credentials-*method* problem — password auth is correct. |
| `Unexpected property "//"` (or JSON parse error) | Comment keys / trailing comma in the credentials file | `.df-credentials.json` is strict JSON — remove them. |
| Prepared-statement / DDL errors mid-run | Using the **transaction** pooler (port 6543) | Use **session** mode (port 5432) or the direct connection. |

## Next steps

- **Using Claude Code?** Install the `sqlanvil-toolkit` plugin so the assistant writes correct
  SQLAnvil code (not Dataform/BigQuery guesses) — see [Using SQLAnvil with Claude Code](./claude-code.md).
- Add real models: tables, incremental tables, views, materialized views, assertions, and
  `operations` (functions/procedures). See the configuration reference.
- Supabase-native features: RLS policies, Realtime publications, and pgvector indexes.
- Use `--schema-suffix dev` to build into a `<schema>_dev` sandbox while developing (declared
  sources are exempt, so dev runs still read your real sources).

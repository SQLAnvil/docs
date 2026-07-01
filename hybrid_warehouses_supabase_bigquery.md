# Hybrid Warehouse Architecture: Supabase & BigQuery

This document outlines how **SQLAnvil** supports both **PostgreSQL (Supabase)** and **Google BigQuery**, and details the architectural patterns for utilizing both warehouses together as sources, targets, or coexisting elements in your data stack.

> For the hands-on version of Pattern C below — reading a BigQuery (or second Postgres) table as a
> live foreign table — see **[Named Connections](./named-connections.md)**. SQLAnvil now
> auto-generates the FDW bridge from a `connections:` entry; you no longer hand-write the wrapper.

---

## 1. Multi-Warehouse Coexistence in SqlAnvil

SqlAnvil is architected around a unified interface, the **`IDbAdapter`** (defined in [cli/api/dbadapters/index.ts](file:///Users/ivan/projects-ivan/sqlanvil/cli/api/dbadapters/index.ts)). This design allows multiple database clients to coexist in the codebase without conflict.

```
       [ SqlAnvil CLI / Core Engine ]
                     │
            ┌────────┴────────┐
      ( warehouse:      ( warehouse: 
      "bigquery" )      "postgres" )
            │                 │
            ▼                 ▼
     [ BigQueryAdapter ] [ PostgresAdapter ]
            │                 │
            ▼                 ▼
     [ Google Cloud ]    [ Supabase / PG ]
```

* **Dynamic Instantiation:** The CLI reads your project's `workflow_settings.yaml` config. Depending on the `warehouse:` configuration, it dynamically instantiates either the `BigQueryDbAdapter` or `PostgresDbAdapter` to execute the compiled SQL graph.
* **No Code Bloat:** Adapters are isolated from each other. You can safely build and scale both database engines inside the same command-line tool.

---

## 2. Hybrid Pipeline Design Patterns

While an individual SqlAnvil execution targets a single connection, you can seamlessly combine Supabase and BigQuery to build powerful hybrid analytics stacks.

### Pattern A: BigQuery Federated Queries (Supabase &rarr; BigQuery)
This is the most direct way to model Supabase data inside a BigQuery analytical warehouse without building separate ETL pipelines.

1. **Setup:** Create a secure **Cloud SQL External Connection** in your Google Cloud Platform console pointing directly to your Supabase PostgreSQL database.
2. **Execution:** Configure your SqlAnvil project to target **BigQuery**.
3. **Modeling:** In your `.sqlx` files, query live Supabase tables in real-time using BigQuery's native `EXTERNAL_QUERY` dialect:
   ```sql
   config {
     type: "table",
     name: "modeled_users"
   }

   SELECT 
     user_id,
     email,
     TIMESTAMP(created_at) as created_at,
     CURRENT_TIMESTAMP() as synchronized_at
   FROM EXTERNAL_QUERY(
     "your-gcp-project.us.supabase-connection-id", 
     "SELECT id as user_id, email, created_at FROM auth.users;"
   )
   ```
* **Pros:** Real-time data access, zero-ETL setup, runs fully inside BigQuery's highly scalable compute layer.

---

### Pattern B: Sequential Multi-Warehouse Modeling
For larger apps, it is often optimal to run lightweight schema cleanups locally on the transactional database before running heavy analytical pipelines in the cloud.

1. **Transactional Stage (Supabase):**
   * Configure a SqlAnvil project targeting **PostgreSQL**.
   * Run pipelines directly on your Supabase DB to clean operational tables, pre-aggregate metrics (e.g. daily transaction summaries), and keep transactional views fast.
2. **Replication Stage:**
   * Replicate the structured transactional summaries from Supabase to Google BigQuery (using tools like Airbyte, Fivetran, or simple scheduled script transfers).
3. **Analytical Stage (BigQuery):**
   * Run a separate SqlAnvil pipeline targeting **BigQuery** to perform deep BI analysis, machine learning model integrations, or historical trend analysis on top of the replicated summaries.

---

### Pattern C: Supabase Wrappers (BigQuery &rarr; Supabase)
If your application needs to display high-level analytical results (computed in BigQuery) to end-users inside the live Supabase app, you can use **Supabase Wrappers** (Postgres Foreign Data Wrappers).

1. **Model in BigQuery:** Use SQLAnvil to process high-compute metrics in BigQuery (e.g., predicting customer churn risk or computing multi-month cohort retention).
2. **Expose in Supabase:**
   * Declare the BigQuery table as a [named connection](./named-connections.md) — SQLAnvil enables the wrapper, creates the foreign server, and exposes a `ref()`-able foreign table for you (no hand-written wrapper).
   * **Cross-project sources (1.13.0):** BigQuery bills the FDW query to the server's project. To read a dataset you can read but not bill (e.g. `bigquery-public-data`), set `billingProject` on the connection to your own GCP project — SQLAnvil bills yours and reads the source via a full-FQN subquery (SA needs `bigquery.jobUser` on the billing project). Without it, the query 403s with `bigquery.jobs.create` denied.
   * Downstream SQLAnvil models join or materialize it like any other source.
   * Your frontend web/mobile application can query the result instantly using standard Supabase client SDKs (`supabase.from('churn_predictions')`).

---

## 3. Comparative Matrix: Supabase (PostgreSQL) vs. BigQuery

| Dimension | PostgreSQL (Supabase) | Google BigQuery |
| :--- | :--- | :--- |
| **Primary Use Case** | Real-time transactional app database (OLTP) | Large-scale analytics and data warehousing (OLAP) |
| **SqlAnvil Adapter** | `PostgresDbAdapter` (uses node-postgres) | `BigQueryDbAdapter` (uses `@google-cloud/bigquery`) |
| **Storage & Cost** | Provisioned disk space; fixed monthly costs | Pay-per-query scan & serverless storage |
| **Dialect** | Standard ANSI PostgreSQL | Google Standard SQL |
| **Execution Scale** | Excellent for thousands of quick reads/writes | Excellent for millions/billions of row transformations |

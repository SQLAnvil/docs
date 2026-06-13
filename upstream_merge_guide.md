# Upstream Merge & Sync Guide: dataform-co/dataform → sqlanvil

This guide outlines a highly robust, sandboxed process for pulling, merging, and resolving conflicts when syncing changes from Google's upstream `dataform-co/dataform` releases (such as `3.0.58`) into the **SQLAnvil** codebase.

---

## 0. Last Sync & The One Key Principle

**Last synced:** upstream **`3.0.59`** → `main` on 2026-06-03 (merge commit `dfeb1e5d`). The local `dataform` branch mirrors the upstream release tag; each sync advances `main`'s merge-base with upstream, so the next merge only sees *new* commits.

> **The #1 lesson: the Bazel build — not grep — is the source of truth for rename leftovers.** Git auto-merges most upstream changes cleanly, but those auto-merged regions carry upstream `dataform.` / `df/` / `@dataform/` / `__dataform_*` tokens that produce **no conflict markers** yet still break the renamed fork. `tsc` (via Bazel) flags every one. Grep is unreliable here: macOS BSD grep silently no-ops on `\bword\b` and on `-r` given an explicit file list. **Finish every sync by building, fixing what tsc reports, and rebuilding — never trust a clean grep alone.**

---

## 1. Upstream Sync Process Architecture

To ensure your primary local `main` branch remains 100% stable during the merge, **always perform the merge inside a temporary sandbox branch** before merging back into `main`.

```
                   upstream/main (Google)
                         │
                         ├── (Tagged Release: e.g. 3.0.58)
                         ▼
             [1. Fetch tag over HTTPS]
                         │
                         ▼
            [2. Create sandbox branch]
           `upstream-sync/dataform-3.0.58`
                         │
                         ▼
          [3. Execute Merge & Resolve Conflicts]
          - Mechanical import renames (df/ → sa/)
          - Proto packages (dataform → sqlanvil)
                         │
                         ▼
              [4. Run Bazel Verification]
             `./scripts/docker-bazel test //...`
                         │
                         ▼
              [5. Merge back to main]
```

---

## 2. Step-by-Step Execution Guide

### Step 1: Fetch Upstream Releases
Ensure your `upstream` remote is configured using HTTPS (to unblock sandbox egress) and fetch all tags:
```bash
# 1. Update upstream URL to HTTPS
git remote set-url upstream https://github.com/dataform-co/dataform.git

# 2. Fetch latest releases & tags
git fetch upstream --tags
```

### Step 2: Create a Sandbox Sync Branch
Checkout a fresh sandbox branch from your local stable `main` branch:
```bash
git checkout main
git checkout -b upstream-sync/3.0.58
```

### Step 3: Run the Merge
Attempt to merge the targeted release tag (e.g. `3.0.58`) into the sandbox:
```bash
git merge 3.0.58
```

---

## 3. Anticipated Conflicts & Resolution Playbook

Since the Dataform $\rightarrow$ SQLAnvil rename touches namespaces and import paths, the merge will trigger a small number of predictable conflicts. Use this playbook to resolve them:

### A. Conflict Type: Protobuf Packages (`protos/core.proto`)
**Conflict:** Upstream adds new protobuf fields inside `package dataform;` whereas SQLAnvil uses `package sqlanvil;`.
* **Resolution:**
  - Keep SQLAnvil's package declaration: `package sqlanvil;`.
  - Copy the new fields added by Google (e.g., `string jit_code = ...` inside `Assertion` message) and insert them using SQLAnvil naming conventions.

### B. Conflict Type: TypeScript Imports (`df/` vs `sa/`)
**Conflict:** Upstream imports use `df/`, e.g.:
```typescript
import { ActionBuilder } from "df/core/actions";
```
SQLAnvil uses `sa/`:
```typescript
import { ActionBuilder } from "sa/core/actions/base";
```
* **Resolution:**
  - Standardize all new/merged imports to use the `sa/` prefix.

### C. Conflict Type: Code References (`dataform.` vs `sqlanvil.`)
**Conflict:** Upstream TypeScript code references Google's generated proto namespace `dataform.Assertion`, while SQLAnvil uses `sqlanvil.Assertion`.
* **Resolution:**
  - Globally replace the merged references to use `sqlanvil.` instead of `dataform.`.

### D. Conflict Type: Auto-merged regions reintroduce `dataform` tokens (the silent one)
**Not a git conflict.** Upstream code that auto-merges cleanly — new helper bodies, new test cases, files upstream rewrote wholesale — arrives carrying `dataform.X`, `df/…` imports, `@dataform/*`, or `__dataform_current_file`. No conflict markers, but it compile-breaks the fork.
* **Resolution:**
  - After resolving the *visible* conflicts, build and rename every token `tsc` reports:
    `./scripts/docker-bazel build //core/... //cli/... //protos/... --jobs=2 --local_ram_resources=2048`
  - When upstream **rewrote a whole file's apparatus** (e.g. `cli/vm/compile.ts` caller-file machinery in 3.0.59), don't resolve hunk-by-hunk — `git checkout --theirs <file>`, then re-apply the rename. Piecemeal resolution leaves auto-merged code referencing variables only the upstream side defines (e.g. `coreBundlePath`, `needsCallerFileShim`).

### E. Specific landmines seen in the 3.0.59 sync
- **Caller-file global:** the exposed sandbox global must stay `__sqlanvil_current_file` (read by `core/utils.ts`); rename upstream's `__dataform_current_file` to it. `__df_enter/__df_exit/__df_current` are internal helper names — fine to leave.
- **`dataform.json` clean break:** never reintroduce the `hasDataformJson` / `global.dataformJson` handling upstream adds — SQLAnvil reads only `workflow_settings.yaml`.
- **Extracted helpers:** when upstream moves logic into helpers (e.g. `executionSql.createTableTasks/Operation/Assertion`), the rename must follow into the auto-merged helper bodies; prior SQLAnvil behavior (e.g. `disabled`-action handling) is usually preserved *inside* them — verify rather than re-add.
- **CLI install-path tests:** upstream tests that `npm i @dataform/core@<ver>` become `@sqlanvil/core@<ver>` (unpublished) — they compile but fail at runtime. Skip or adapt; don't let them block the sync.
- **`df_` in generated SQL / test fixtures:** watch for non-namespace leftovers like `df_osc_`, `_df_temp_`, `df_integration_test` → rename to `sa_`. Casing artifacts too (`readsqlanvil…` → `readSqlanvil…`).

---

## 4. Verification & Clean-Up

Once all conflicts are resolved, run the full validation suite inside your development container. **Native macOS Bazel is broken** (the `wrapped_clang` / dyld `LC_UUID` toolchain error compiling protobuf C++), so all builds/tests go through `scripts/docker-bazel`. Always pass `--jobs=2 --local_ram_resources=2048` — the in-container Bazel JVM gets OOM-killed (`Socket closed`, error 14) under default parallelism during webpack bundling.

```bash
# 1. Build everything affected — THIS is what catches reintroduced dataform tokens
./scripts/docker-bazel build //core/... //cli/... //protos/... --jobs=2 --local_ram_resources=2048

# 2. Run core compiler tests
./scripts/docker-bazel test //core/... --jobs=2 --local_ram_resources=2048

# 2. Run the newly updated integration tests
PG_HOST=host.docker.internal PG_PORT=5432 ./scripts/docker-bazel test //tests/integration:postgres.spec --test_env=PG_HOST --test_env=PG_PORT --test_env=PG_USER --test_env=PG_PASSWORD --test_env=PG_DATABASE
```

If the build completes and all tests pass:
```bash
# 3. Checkout main & merge the verified sync branch
git checkout main
git merge upstream-sync/3.0.58

# 4. Clean up the sandbox branch
git branch -d upstream-sync/3.0.58

# 5. Push updated main to origin (GitHub)
git push origin main
```

## 5. Local fixes reported upstream (converge on adoption)

Bugs we fixed locally that are **also present upstream** and that we've reported to
`dataform-co/dataform`. On each sync, check whether upstream adopted the fix — if so, take
*their* version and drop our local change so the file stops diverging (less future merge friction).
If not, keep ours and re-apply over the merge.

| File | Local fix | Upstream issue / PR | Status |
| :--- | :--- | :--- | :--- |
| `common/flags/index.ts` | Lenient arg parser — ignore non-flag tokens instead of throwing `Arg neither flag name nor flag value` (which crashed the CLI when a positional followed a flag). Extracted `parseArgvFlags()` + test. | issue [dataform-co/dataform#2198](https://github.com/dataform-co/dataform/issues/2198) → fixed by PR [#2199](https://github.com/dataform-co/dataform/pull/2199) | PR open, not merged (not on `main`, not tagged) — watch #2199 for the convergence trigger |

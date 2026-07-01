# npm Publishing

**Status:** Reference / runbook
**Last updated:** 2026-06-03

## 0. TL;DR

- npm organization: **`sqlanvil`** (free tier, public packages)
- Placeholders published 2026-05-27: `@sqlanvil/cli@0.0.1`,
  `@sqlanvil/core@0.0.1` (name reservations only)
- **Versioning is sqlanvil's own SemVer line: `SQLANVIL_VERSION`** in `version.bzl`
  (currently **1.0.0**). The Bazel build stamps the npm packages from it.
  `DF_VERSION` (currently **3.0.59**) is the upstream dataform release this fork is
  synced to — kept as **metadata** (surfaced in `sqlanvil --version` as
  `sqlanvil 1.0.0 (Dataform core 3.0.59)` and exported as `dataformVersion` from
  `@sqlanvil/core`), **not** the package version. Bump `SQLANVIL_VERSION` for
  sqlanvil releases; bump `DF_VERSION` on upstream syncs. See §4.
- Real publish target: Bazel-built tarball at
  `bazel-bin/packages/@sqlanvil/cli/package.tar.gz` (and matching for `core`)
- **For testing, don't publish** — install the built tarball locally
  (`npm i ./sqlanvil-cli.tgz`). Only publish to npm when you specifically
  need the install-from-registry path (the stateless `sqlanvilCoreVersion`
  flow), and then publish a **`-beta.N` prerelease under `--tag beta`** so
  you never burn a real version number. See §3.0.

## 1. npm Organization

- **Name:** `sqlanvil`
- **Owner:** Ivan Histand (`ihistand` on npm)
- **Plan:** Free (unlimited public packages, no private packages)
- **Created:** 2026-05-27
- **URL:** https://www.npmjs.com/org/sqlanvil

Free plan is sufficient — sqlanvil is OSS, no private packages needed.

## 2. Published Packages

| Package | Version | Date | Status |
| :--- | :--- | :--- | :--- |
| `@sqlanvil/cli` | `0.0.1` | 2026-05-27 | placeholder (name reservation) |
| `@sqlanvil/core` | `0.0.1` | 2026-05-27 | placeholder (name reservation) |
| `@sqlanvil/core` | `1.4.0` | 2026-06-16 | real — compile node selection |
| `@sqlanvil/cli` | `1.4.0` | 2026-06-16 | real — compile node selection |
| `@sqlanvil/core` | `1.4.1` | 2026-06-16 | real — Postgres index-name + client-release fixes |
| `@sqlanvil/cli` | `1.4.1` | 2026-06-16 | real — Postgres index-name + client-release fixes |
| `@sqlanvil/core` | `1.5.0` | 2026-06-17 | real — MySQL/MariaDB adapter |
| `@sqlanvil/cli` | `1.5.0` | 2026-06-17 | real — MySQL/MariaDB adapter |
| `@sqlanvil/core` | `1.6.0` | 2026-06-17 | real — MySQL `mysql:{}` block + COMMENT metadata + matview emulation |
| `@sqlanvil/cli` | `1.6.0` | 2026-06-17 | real — MySQL `mysql:{}` block + COMMENT metadata + matview emulation |
| `@sqlanvil/core` | `1.7.0` | 2026-06-18 | real — `--environment` named environments |
| `@sqlanvil/cli` | `1.7.0` | 2026-06-18 | real — `--environment` named environments |
| `@sqlanvil/core` | `1.8.0` | 2026-06-18 | real — `type:"export"` file exports |
| `@sqlanvil/cli` | `1.8.0` | 2026-06-18 | real — `type:"export"` file exports (+ duckdb dep) |
| `@sqlanvil/core` | `1.8.1` | 2026-06-19 | real — multiline `rowConditions` BigQuery fix (#42) |
| `@sqlanvil/cli` | `1.8.1` | 2026-06-19 | real — multiline `rowConditions` BigQuery fix (#42) |
| `@sqlanvil/core` | `1.8.2` | 2026-06-23 | real — upstream 3.0.60 sync: `AssertionConfig.metadata` (#2208) + vm2/protobufjs bumps |
| `@sqlanvil/cli` | `1.8.2` | 2026-06-23 | real — upstream 3.0.60 sync: `AssertionConfig.metadata` (#2208) + vm2/protobufjs bumps |
| `@sqlanvil/core` | `1.8.3` | 2026-06-24 | real — MySQL pre/post-ops (#44) + DuckDB → `@duckdb/node-api` security migration (#40) |
| `@sqlanvil/cli` | `1.8.3` | 2026-06-24 | real — MySQL pre/post-ops (#44) + DuckDB → `@duckdb/node-api` security migration (#40) |
| `@sqlanvil/core` | `1.9.0` | 2026-06-24 | real — `sqlanvil validate` (pre-execution SQL validation, all 4 warehouses) + run --dry-run footgun fix |
| `@sqlanvil/cli` | `1.9.0` | 2026-06-24 | real — `sqlanvil validate` (pre-execution SQL validation, all 4 warehouses) + run --dry-run footgun fix |
| `@sqlanvil/core` | `1.10.0` | 2026-06-24 | real — queryable Parquet artifacts + `query`/`inspect`/`docs` |
| `@sqlanvil/cli` | `1.10.0` | 2026-06-24 | real — queryable Parquet artifacts + `query`/`inspect`/`docs` |
| `@sqlanvil/core` | `1.11.0` | 2026-06-25 | real — MySQL native partitioning (#45) + MySQL `introspect` (#35) |
| `@sqlanvil/cli` | `1.11.0` | 2026-06-25 | real — MySQL native partitioning (#45) + MySQL `introspect` (#35) |
| `@sqlanvil/core` | `1.12.0` | 2026-06-26 | real (`latest`) — `type:"import"` file import (DuckDB bridge / BigQuery LOAD DATA) |
| `@sqlanvil/cli` | `1.12.0` | 2026-06-26 | real (`latest`) — `type:"import"` file import (DuckDB bridge / BigQuery LOAD DATA) |

The `0.0.1` rows are the original name-reservation placeholders (source not
committed — one-shot scaffolding under `~/sqlanvil-npm-placeholders/` on Ivan's
Mac); they were superseded by the first real `1.x` publish. The current
published version of both packages is **`1.12.0`** (`latest`).

> **Gotcha (learned on 1.5.0):** the publishable tarball target is
> `//packages/@sqlanvil/{core,cli}:package_tar` (produces `package.tar.gz`), **not**
> `:package` (the `pkg_npm` dir). Building only `:package` regenerates the dir but
> leaves a **stale `package.tar.gz`** from the previous version in the bazel volume,
> so the extracted tarball ships the old version. Always build `:package_tar`.

### Names to consider claiming later

Don't grab speculatively (npm anti-squatting policy may reclaim dormant
placeholders after 6+ months). Claim when implementation is within 1–2
phases of needing them:

| Package | Probable timing | Purpose |
| :--- | :--- | :--- |
| `@sqlanvil/postgres` | Phase 3b–4 | If Postgres adapter ships standalone |
| `@sqlanvil/supabase` | Phase 5 | Supabase variant adapter |
| `@sqlanvil/protos` | Phase 4 | Generated TS proto types as standalone module |
| `sqlanvil` (unscoped) | optional | Vanity name — easier `npm i sqlanvil` |

## 3. Workflow

### 3.0. Testing without publishing (preferred)

For almost all testing you do **not** need npm. Build the tarball and
install it locally — it's the identical artifact you'd publish, with zero
registry impact:

```bash
cd ~/projects-ivan/sqlanvil
./scripts/docker-bazel build //packages/@sqlanvil/cli:package_tar \
                             //packages/@sqlanvil/core:package_tar \
                             --jobs=2 --local_ram_resources=2048

# Extract from the Docker bazel volume to the host (see §3.2), then in a
# throwaway test project:
npm i /path/to/sqlanvil-core.tgz
npm i -g /path/to/sqlanvil-cli.tgz
```

The one thing a local install can't exercise: the **stateless install**
path, where `cli/api/commands/compile.ts` runs `npm i @sqlanvil/core@<ver>`
from the registry (triggered by `sqlanvilCoreVersion:` in
`workflow_settings.yaml`). To validate that flow end-to-end the package must
be on npm — publish a beta (§3.4), never the real version, for testing.

### 3.1. Build the tarball

**As of 2026-07-01, native macOS Bazel works again** — build directly:

```bash
cd ~/projects-ivan/sqlanvil
bazel build //packages/@sqlanvil/cli:package_tar //packages/@sqlanvil/core:package_tar
# Output: bazel-bin/packages/@sqlanvil/{cli,core}/package.tar.gz (real path, cp-able from the host)
```

The old `wrapped_clang`/dyld `LC_UUID` protobuf-C++ breakage self-resolved via a macOS/CLT update
(`.bazelversion` is unchanged at 5.4.0). Verified with a forced `--compilation_mode=opt` recompile.

**Fallback — `scripts/docker-bazel` (Linux toolchain)**, kept for CI/reproducibility or if native breaks
again on a future macOS:

```bash
cd ~/projects-ivan/sqlanvil
./scripts/docker-bazel build //packages/@sqlanvil/cli:package_tar \
                             --jobs=2 --local_ram_resources=2048
# Output: bazel-bin/packages/@sqlanvil/cli/package.tar.gz
```

`--jobs=2 --local_ram_resources=2048` avoids OOM-killing the in-container
Bazel JVM during the webpack bundling step.

The tarball is platform-agnostic — pure JS bundle, no native deps in the
tarball itself. Native modules like `pg` are listed in `package.json` and
rebuilt by npm on the consumer's machine.

### 3.2. Extract from container to host

`bazel-bin/` symlinks into the Docker named volume, so you can't `cp` from
the host. Run the copy inside a container with the workspace mounted:

```bash
docker run --rm \
  -v "$PWD:/workspace" \
  -v sqlanvil-bazel-cache:/root/.cache/bazel \
  -v sqlanvil-bazel-disk:/root/.cache/bazel-disk \
  sqlanvil-dev \
  cp /workspace/bazel-bin/packages/@sqlanvil/cli/package.tar.gz \
     /workspace/sqlanvil-cli.tgz
```

Tarball now at `./sqlanvil-cli.tgz` (host filesystem). Repeat for `core`.

### 3.3. Inspect before publishing

```bash
tar tzf sqlanvil-cli.tgz | head -20
# Should show: package/package.json, package/bundle.js, etc.

tar xzf sqlanvil-cli.tgz -C /tmp/inspect && cat /tmp/inspect/package/package.json
# Verify name + version are what you expect (version == SQLANVIL_VERSION, e.g. 1.0.0)
```

### 3.4. Publish

```bash
npm login                                  # 2FA device-auth flow (see §6)

# Real release (version == SQLANVIL_VERSION, e.g. 1.0.0):
npm publish ./sqlanvil-cli.tgz --access public

# Beta / test release (set SQLANVIL_VERSION = "1.0.0-beta.0" first, rebuild):
npm publish ./sqlanvil-cli.tgz --access public --tag beta
```

`--tag beta` keeps the prerelease off the `latest` dist-tag, so
`npm i @sqlanvil/cli` users never receive it; testers opt in with
`npm i @sqlanvil/cli@beta`. Publish `core` first, then `cli`.

### 3.5. Tag + GitHub release (real releases only) — **use the script**

npm is the primary channel, but the GitHub **Releases** page is the human-facing
changelog and must be updated too — it does not auto-update from npm. **This step
was forgotten on 1.8.2 AND 1.13.0** (GitHub kept showing the prior version until
noticed), so it's automated:

```bash
cd ~/projects-ivan/sqlanvil
./scripts/release_github            # releases the version in version.bzl
# DRY_RUN=1 ./scripts/release_github   # preview first (touches nothing)
```

The script reads `SQLANVIL_VERSION`, pulls the title + body from the
`sqlanvil-com` `whats-new.md` `## X.Y.Z` section (absolutising `/docs` links and
appending the Install line), finds the `release: SQLAnvil X.Y.Z` commit, creates
+ pushes the canonical **`vX.Y.Z`** tag, and runs `gh release create … --latest`.
It's **idempotent** — re-run to fix a title/notes typo (it updates the existing
release). **Prereq:** add the release's `## X.Y.Z` entry to `whats-new.md` first
(part of the docs step this runbook already requires).

**Tag convention:** `vX.Y.Z` is sqlanvil's canonical release tag. The bare
`X.Y.Z` tags (up to 1.22.2, past our current version) are **inherited upstream
Dataform tags** — leave them; do not delete or release from them.

Manual fallback (what the script runs), if `gh`/whats-new isn't available:

```bash
git tag -a vX.Y.Z <release-commit> -m "vX.Y.Z — <short desc>"
git push origin vX.Y.Z
gh release create vX.Y.Z --repo SQLAnvil/sqlanvil --verify-tag --latest \
  --title "vX.Y.Z — <short desc>" --notes-file <notes.md>
```

(`gh release create --target <short-sha>` fails with "target_commitish is
invalid" — push the tag first, then create the release with `--verify-tag`.)

## 4. Version Policy

**sqlanvil has its own SemVer line, `SQLANVIL_VERSION`** (in `version.bzl`,
currently `1.0.0`) — this is the published `@sqlanvil/*` version. **`DF_VERSION`**
(currently `3.0.59`) is the upstream `dataform-co/dataform` release the fork is
synced to, kept as **metadata** only: surfaced in `sqlanvil --version`
(`sqlanvil 1.0.0 (Dataform core 3.0.59)`) and exported as `dataformVersion` from
`@sqlanvil/core`. The two are independent (you can't express "our build N on top
of 3.0.59" in 3-segment SemVer like `3.0.59.1`, so the package version is decoupled).

Why decouple (supersedes the earlier "track `DF_VERSION`" policy): SemVer is three
segments, so pinning the package to the exact upstream patch leaves **no room for
sqlanvil's own iterative releases** between upstream syncs. `@sqlanvil/core` is a
different npm package than `@dataform/core`, so there is no version collision to
avoid — mirroring `DF_VERSION` only ever helped human signaling, which the
`--version` / `dataformVersion` metadata now provides.

The CLI's compile-time `cli/core` compatibility check derives its floor from the
**CLI's own version** (`cli/vm/compile.ts` requires a matching major + core ≥
`cli.major.minor.0`), so cli@`X` + core@`X` (both stamped from `SQLANVIL_VERSION`)
stay in lockstep automatically. Dataform-capability gates inside the SQL
generators (e.g. incremental pre/post-ops at `> 1.4.8`, the pre-3.0.57 caller-file
shim) compare against the **Dataform** version (`dataformVersion`), not the package
version — so decoupling does not disable them.

- **sqlanvil release:** bump `SQLANVIL_VERSION` in `version.bzl` (e.g. `1.0.0` →
  `1.0.1` / `1.1.0`).
- **Upstream sync:** bump `DF_VERSION` (the `scripts/update_version` helper) — this
  changes the displayed Dataform base, not the package version.
- **Prerelease / test:** `SQLANVIL_VERSION = "X.Y.Z-beta.N"`, published `--tag beta`.
  Never reuse a real version number — npm versions are permanent and immutable.
- **Don't publish at all for local testing** — use §3.0.

## 5. Future: Automate via CI

Once GitHub Actions is wired, publish becomes a tag-triggered CI step:

```yaml
# .github/workflows/release.yml
on:
  push:
    tags: ['v*']
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./scripts/docker-bazel build //packages/@sqlanvil/cli:package_tar
      - run: docker cp $(docker create sqlanvil-dev):/workspace/bazel-bin/packages/@sqlanvil/cli/package.tar.gz ./pkg.tgz
      - uses: actions/setup-node@v4
        with:
          registry-url: https://registry.npmjs.org
      - run: npm publish ./pkg.tgz --access public
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Token setup: npm → Profile → Access Tokens → Generate (Granular, scoped to
`@sqlanvil/*`) → store as `NPM_TOKEN` GitHub secret.

## 6. Auth Notes

- 2FA is enabled by default for new npm accounts. Each `npm publish` from a
  CLI triggers a browser device-auth flow.
- For automation, use **granular access tokens** (npm Profile → Access Tokens
  → "Granular") scoped only to `@sqlanvil/*` packages. Less blast radius than
  classic legacy tokens.

## 7. References

- [npm scopes docs](https://docs.npmjs.com/cli/v10/using-npm/scope)
- [npm publishing policy](https://docs.npmjs.com/policies/unpublish)
- [Anti-squatting policy](https://docs.npmjs.com/policies/disputes)
- `upstream_merge_guide.md` — how upstream syncs land on `main`
- `postgres_first_class_design.md` §9 — phase plan

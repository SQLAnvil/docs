# npm Publishing

**Status:** Reference / runbook
**Last updated:** 2026-06-03

## 0. TL;DR

- npm organization: **`sqlanvil`** (free tier, public packages)
- Placeholders published 2026-05-27: `@sqlanvil/cli@0.0.1`,
  `@sqlanvil/core@0.0.1` (name reservations only)
- **Versioning tracks `DF_VERSION`** in `version.bzl` — i.e. the upstream
  dataform release the fork is synced to (currently **3.0.59**). The Bazel
  build stamps both packages from `DF_VERSION`; there is no separate
  sqlanvil version number. See §4.
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

Both placeholders print "not yet published — see GitHub" and exit
non-zero. Source not committed — one-shot scaffolding under
`~/sqlanvil-npm-placeholders/` on Ivan's Mac.

> **Note:** The next real publish will be **3.x** (DF_VERSION parity), not
> `0.0.2`. This is a deliberate jump — see §4. The `0.0.1` placeholders are
> superseded the first time a real `3.x` build is published.

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

### 3.1. Build the tarball inside Docker

Native macOS Bazel is currently broken on this host (the `wrapped_clang`
toolchain hits a dyld `LC_UUID` error when compiling protobuf's C++), so all
builds go through `scripts/docker-bazel` (Linux toolchain):

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
# Verify name + version are what you expect (version == DF_VERSION)
```

### 3.4. Publish

```bash
npm login                                  # 2FA device-auth flow (see §6)

# Real release (version == DF_VERSION, e.g. 3.0.59):
npm publish ./sqlanvil-cli.tgz --access public

# Beta / test release (set DF_VERSION = "3.0.59-beta.0" first, rebuild):
npm publish ./sqlanvil-cli.tgz --access public --tag beta
```

`--tag beta` keeps the prerelease off the `latest` dist-tag, so
`npm i @sqlanvil/cli` users never receive it; testers opt in with
`npm i @sqlanvil/cli@beta`. Publish `core` first, then `cli`.

## 4. Version Policy

**sqlanvil versions track `DF_VERSION`** (in `version.bzl`), which equals the
upstream `dataform-co/dataform` release the fork is synced to. A published
`@sqlanvil/core@3.0.59` means "dataform 3.0.59 + the sqlanvil rename +
Postgres/Supabase work." This gives users a legible parity signal and keeps
core/cli in lockstep.

Why parity, not an independent `0.x` line: the CLI enforces its own
core/cli compatibility at compile time (`cli/vm/compile.ts` requires
`@sqlanvil/core ^3.0.0` and a cli/core **major.minor match**). An
independent `0.x` core would make the CLI reject its own core. So the real
floor is `3.x`, and tracking `DF_VERSION` is the simplest scheme that
satisfies it.

- **Real release:** version == `DF_VERSION` (e.g. `3.0.59`). Bump by syncing
  upstream / editing `version.bzl`.
- **Prerelease / test:** `DF_VERSION = "X.Y.Z-beta.N"`, published `--tag beta`.
  Never reuse a real version number for a throwaway test publish — npm
  versions are permanent and immutable.
- **Don't publish at all for local testing** — use §3.0.

> This supersedes the earlier `0.0.x → 0.x.y → 1.0.0` policy, which predated
> the decision to track upstream versions and conflicts with the CLI's
> `^3.0.0` self-check.

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

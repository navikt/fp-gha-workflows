# fp-gha-workflows

Reusable GitHub Actions workflows and composite actions for
[`@navikt/teamforeldrepenger`](https://github.com/navikt) — used by ~all 40+
foreldrepenge-/fp-repos.

Shared team context lives in [`navikt/fp-context`](https://github.com/navikt/fp-context).
This README is the catalog for the workflows and actions; the canonical
**pinning & ratchet policy** is documented in
[`fp-context/operations/ci-cd.md`](https://github.com/navikt/fp-context/blob/main/operations/ci-cd.md#workflow-pinning--ratchet-policy)
and applies to every workflow in every fp-repo.

## Reusable workflows (`.github/workflows/`)

Triggered via `workflow_call`.

| Workflow | Purpose | Key inputs |
|----------|---------|------------|
| `build-app-no-db.yml` | Maven build + optional Docker image build/push, no DB | `java-version`, `build-image`, `push-image`, `use-reader` |
| `build-docker-bake.yml` | Maven build + Docker Bake multi-platform image + tests | `bake-target` (req), `bake-platform`, `push-image` |
| `build-feature.yml` | Build feature branch (no deploy) | `java-version`, `t-2c`, `use-reader`, `working-directory` |
| `codeql.yml` | CodeQL static analysis (+ optional Sonar) | `language`, `sonar`, `java-version` |
| `deploy.yml` | Deploy image to a NAIS cluster | `image` (req), `cluster` (req), `namespace`, `naiserator_file`, `gar` |
| `deploy-storybook.yml` | Build + deploy Storybook to GitHub Pages | `package-manager` (req), `cache` (req) |
| `mvn-dependency-submission.yml` | Submit Maven dep graph to GitHub Dependency Submission API | `java-version` |
| `release-drafter.yml` | Auto-draft release notes | — |
| `release-feature.yml` | Release artifact from a feature branch | `release-version` (req), `release-branch`, `release-profiles` |

## Composite actions (`.github/actions/`)

Reusable steps for use inside any workflow.

| Action | Purpose | Key inputs |
|--------|---------|------------|
| `build-maven-application` | Maven install with cache + build-version output | `t-2c`, `profil` |
| `build-push-docker-image` | Build + push a Docker image | `build-version` (req), `dockerfile`, `push-image` |
| `build-push-docker-bake` | Build + push via Docker Bake (multi-arch) | `build-version` (req), `bake-target` (req), `github-token` (req) |
| `knip-it` | Frontend unused-export detection (Knip) | `npm-auth-token` (req), `package-manager` (req), `github-token` (req) |
| `setup-npmrc` | npm registry config for `@navikt/*` packages | `npmAuthToken` (req) |
| `setup-yarnrc` | Yarn registry config for `@navikt/*` packages | `npmAuthToken` (req) |

## Shared config

- `.github/codeql/codeql-config.yml` — CodeQL config consumed by `codeql.yml`
- `.github/dependabot.yml` — keeps `github-actions` versions current per the
  ratchet policy

## Usage

Reference reusable workflows with `@main # ratchet:exclude`:

```yaml
jobs:
  build:
    uses: navikt/fp-gha-workflows/.github/workflows/build-docker-bake.yml@main # ratchet:exclude
    with:
      bake-target: app
      push-image: true
    secrets: inherit
```

Reference composite actions the same way:

```yaml
steps:
  - uses: navikt/fp-gha-workflows/.github/actions/build-maven-application@main # ratchet:exclude
```

External actions must be pinned to a full commit SHA with a ratchet comment —
see the [pinning policy in fp-context](https://github.com/navikt/fp-context/blob/main/operations/ci-cd.md#workflow-pinning--ratchet-policy).


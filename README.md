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

| Workflow                        | Purpose                                                    | Key inputs                                                            | Secrets                                            |
| ------------------------------- | ---------------------------------------------------------- | --------------------------------------------------------------------- | -------------------------------------------------- |
| `build-app-no-db.yml`           | Maven build + optional Docker image build/push, no DB      | `java-version`, `build-image`, `push-image`, `namespace`              | —                                                  |
| `build-feature.yml`             | Build feature branch (no deploy)                           | `java-version`, `t-2c`, `working-directory`                           | —                                                  |
| `codeql.yml`                    | CodeQL static analysis (+ optional Sonar)                  | `sonar`, `java-version`, `t-2c`                                       | `SONAR_TOKEN` (optional, brukes når `sonar: true`) |
| `deploy.yml`                    | Deploy image to a NAIS cluster                             | `image` (req), `cluster` (req), `namespace`, `naiserator_file`, `gar` | —                                                  |
| `deploy-storybook.yml`          | Build + deploy Storybook to GitHub Pages                   | `package-manager` (req), `cache` (req)                                | —                                                  |
| `mvn-dependency-submission.yml` | Submit Maven dep graph to GitHub Dependency Submission API | `java-version`                                                        | —                                                  |
| `release-drafter.yml`           | Auto-draft release notes                                   | —                                                                     | —                                                  |
| `release-feature.yml`           | Release artifact from a feature branch                     | `release-version` (req), `release-branch`, `release-profiles`         | —                                                  |

Alle workflowene bruker det innebygde `GITHUB_TOKEN` automatisk (via
`permissions:`) — de trenger ikke eksplisitt tilsendte secrets utover det som
står i tabellen. Send kun de secretsene som faktisk er deklarert; unngå
`secrets: inherit`.

## Composite actions (`.github/actions/`)

Reusable steps for use inside any workflow.

| Action                    | Purpose                                         | Key inputs                                                                | Secrets |
| ------------------------- | ----------------------------------------------- | ------------------------------------------------------------------------- | ------- |
| `build-maven-application` | Maven install with cache + build-version output | `t-2c`, `profil`                                                          | —       |
| `build-push-docker-image` | Build + push a Docker image                     | `build-version` (req), `dockerfile` (req), `push-image`, `docker_context` | —       |
| `knip-it`                 | Frontend unused-export detection (Knip)         | `package-manager` (req), `node-version`                                   | —       |
| `setup-yarnrc`            | Yarn registry config for `@navikt/*` packages   | —                                                                         | —       |

Composite actions deklarerer ikke egne secrets — de arver `GITHUB_TOKEN` og
miljøet fra jobben som kaller dem.

## Shared config

- `.github/codeql/codeql-config.yml` — CodeQL config consumed by `codeql.yml`
- `.github/dependabot.yml` — keeps `github-actions` versions current per the
  ratchet policy

## Usage

Reference reusable workflows with `@main # ratchet:exclude`:

```yaml
jobs:
  build:
    uses: navikt/fp-gha-workflows/.github/workflows/build-app-no-db.yml@main # ratchet:exclude
    with:
      build-image: true
      push-image: true
```

Send secrets eksplisitt kun når en workflow deklarerer dem (f.eks. `SONAR_TOKEN`
til `codeql.yml`) — ikke bruk `secrets: inherit`.

Reference composite actions the same way:

```yaml
steps:
  - uses: navikt/fp-gha-workflows/.github/actions/build-maven-application@main # ratchet:exclude
```

External actions must be pinned to a full commit SHA with a ratchet comment —
see the [pinning policy in fp-context](https://github.com/navikt/fp-context/blob/main/operations/ci-cd.md#workflow-pinning--ratchet-policy).

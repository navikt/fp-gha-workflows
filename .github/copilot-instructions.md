# fp-gha-workflows — Copilot instructions

Hub: `navikt/fp-context` (read first) · Space: `navikt/TeamForeldrepenger`.
Only repo-specific deltas below.

## Scope
Reusable workflows + composite actions for ~all 40+ fp-repos.
Exceptions w/ own CI: `fp-autotest`, `fp-iac`, `fp-baseimages`.
Changes here propagate cluster-wide → keep inputs backward-compatible; flag breaking changes in PR.

## Layout
- `.github/workflows/` — `workflow_call` workflows (`build-*`, `release-*`, `deploy*`, `codeql`, ...)
- `.github/actions/` — composite actions
- `.github/codeql/` — shared CodeQL config
- `.github/dependabot.yml` — `github-actions` ecosystem

## `uses:` pinning
Canonical policy: [`fp-context/operations/ci-cd.md#workflow-pinning--ratchet-policy`](https://github.com/navikt/fp-context/blob/main/operations/ci-cd.md#workflow-pinning--ratchet-policy). Applies to every workflow file here and in all fp-repos. Dependabot (`github-actions` ecosystem) honors the ratchet comments.

## Conventions
- Composite steps: `shell: bash`, quote vars.
- Set `permissions:` explicitly, least-privilege.
- New inputs need `default:`; don't change semantics of existing inputs.
- Default bumps (java/node/runner) are intentional — they fan out on next run.
- Keep naming patterns `build-*` / `release-*` / `deploy*`.
- Norwegian (bokmål) prose ok; identifiers English.

## Policy home
Pinning rules are cluster-wide; canonical home is `navikt/fp-context` — update there when policy changes, not here.

## Catalog
Reusable workflows and composite actions are catalogued in [`README.md`](../README.md).

# fp-gha-workflows

Reusable GitHub workflows and composite actions for Team Foreldrepenger repositories.

## Shared context

- Source of truth for cross-cutting workflow policy: `navikt/fp-context`
- Copilot Space: `navikt/TeamForeldrepenger`

Only repo-specific deltas belong here.

## Scope
- Reusable workflows and composite actions for most Team Foreldrepenger repos.
- Notable repos with independent CI setups: `fp-autotest`, `fp-iac`, `fp-baseimages`.
- Changes here fan out broadly. Keep inputs backward-compatible and flag breaking changes clearly in PRs.

## Layout
- `.github/workflows/` — `workflow_call` workflows (`build-*`, `release-*`, `deploy*`, `codeql`, ...)
- `.github/actions/` — composite actions
- `.github/codeql/` — shared CodeQL config
- `.github/dependabot.yml` - local dependabot configuration

## Conventions
- Version pinning as of `fp-context/operations/ci-cd.md#workflow-pinning--ratchet-policy`
- Use explicit `permissions:` with least privilege
- Composite steps use `shell: bash` and quoted variables
- New inputs need defaults; do not silently change semantics of existing inputs
- Keep naming patterns `build-*`, `release-*`, and `deploy*`
- Default version bumps for Java, Node, or runner images fan out to consumers on their next run

## Catalog
Reusable workflows and composite actions are cataloged in `README.md`.

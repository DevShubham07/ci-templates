# ci-templates

Reusable GitHub Actions workflows — the fleet's single CI definition (design doc §4.5).
Sites never define their own CI logic; they call these by tag.

## How a site calls `astro-ci.yml`

Add this as the site repo's entire `.github/workflows/ci.yml` (per `site-template`, §4.4):

```yaml
name: ci
on: [push, pull_request]
jobs:
  build-test-perf:
    uses: ORG/ci-templates/.github/workflows/astro-ci.yml@v1
    secrets: inherit
```

`secrets: inherit` is only needed if org secrets are ever required by CI (design doc §4.5);
omit it if the caller has none to pass. `lighthouserc.json` at the site's repo root is used
automatically if present, otherwise the shared default in this repo (`ci-templates/lighthouserc.json`)
is used.

## Version-tag policy

Callers pin a **major tag** (`@v1`), never `@main` and never a commit SHA, so template/CI fixes
land in every site without per-site edits, while breaking changes require a deliberate `@v2` bump
across the fleet. Tags are moved forward on release (`git tag -f v1 && git push -f origin v1`) for
non-breaking patches; a genuinely breaking workflow change gets a new major tag instead of moving
`v1`. Do not reference `@v2` (or any tag) before it has actually been cut — see design doc §4.3's
note on the Changesets README advertising an unreleased `v2`.

# pinpoint-action

**One line to protect your GitHub Actions supply chain.**

Pinpoint Gate verifies the integrity of every action your workflows depend on — before they execute. If a version tag has been repointed to a malicious commit (as happened to [trivy-action](https://github.com/aquasecurity/trivy/discussions/10425), [tj-actions/changed-files](https://github.com/advisories/ghsa-mrrh-fwg8-r2c3), and [reviewdog/action-setup](https://nvd.nist.gov/vuln/detail/CVE-2025-30154)), Pinpoint catches it.

## Quick Start

```yaml
- uses: tehreet/pinpoint-action@v1
```

That's it. Add this step before your other actions. It downloads the Pinpoint binary and runs `pinpoint gate` against all workflow files in your repository.

## Usage

### Warn mode (default — log violations, don't block)

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: tehreet/pinpoint-action@v1
  # ... rest of your workflow
```

### Enforce mode (block builds on violations)

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: tehreet/pinpoint-action@v1
    with:
      mode: enforce
```

### With a lockfile (recommended)

Generate a lockfile first:

```bash
pinpoint lock --workflows .github/workflows/
```

Commit `.github/actions-lock.json` to your repo. The gate step will verify every action reference against the lockfile and catch any tag repointing.

## Inputs

| Input | Description | Default |
|---|---|---|
| `version` | Pinpoint release version | `v0.6.0` |
| `mode` | `warn` (log only) or `enforce` (fail on violations) | `warn` |
| `all-workflows` | Scan all workflow files, not just the triggering one | `true` |
| `token` | GitHub token for API access | `${{ github.token }}` |

## What it detects

- **Tag repointing** — a version tag moved to a different commit (the Trivy/tj-actions attack vector)
- **Actions not in lockfile** — new dependencies added without updating the lockfile
- **Branch-pinned actions** — mutable refs like `@main` that can change at any time

## Requirements

- A lockfile at `.github/actions-lock.json` (generate with `pinpoint lock`)
- `contents: read` permission on the workflow

## Links

- [Pinpoint](https://github.com/tehreet/pinpoint) — the tool behind this action
- [Actions Watchdog](https://tehreet.github.io/actions-watchdog/) — live integrity monitoring for the top 50 GitHub Actions
- [The Case for Enforcement](https://gist.github.com/tehreet/ded6584acb8c1fba372dd46b9aef9a45) — why SHA pinning alone isn't enough

## License

GPL-3.0-only

# @rhuanbarreto/renovate-config

Shared [Renovate](https://docs.renovatebot.com/) configuration for all repositories in the rhuanbarreto organization.

## Usage

Each repository should have a `renovate.json` containing:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>rhuanbarreto/renovate-config"]
}
```

Repo-specific overrides can be added alongside the `extends` array.

## What's included

### Base configuration

Extends [`config:best-practices`](https://docs.renovatebot.com/presets-config/#configbest-practices), which includes:

- `config:recommended` (dependency dashboard, semantic commits, monorepo grouping)
- Pin devDependencies
- Pin Docker image digests
- Pin GitHub Action digests
- npm minimum release age (overridden to **7 days** -- see below)
- Weekly lock file maintenance
- Config migration PRs

### Security hardening

- **7-day minimum release age** for all updates (overrides the 3-day `config:best-practices` default)
- **OpenSSF Scorecard** badges on PRs for supply-chain visibility
- **OSV vulnerability alerts** enabled
- **Vulnerability PRs** skip the release-age delay and get priority `10`
- **GitHub Actions pinned to SHA digests**
- **Major updates require dashboard approval** before PRs are created

### Noise reduction

- **Non-major devDependencies grouped** into a single PR per repo
- **Docs dependencies grouped** into a single PR
- **GitHub Actions grouped** into a single PR
- **Linters, formatters, and type packages auto-merged** on minor/patch
- **Schedule:** PRs created during non-office hours only
- **Rate limited:** max 5 concurrent PRs, max 2 per hour

### Custom managers -- `.prototools`

Regex custom managers parse tool versions from [proto](https://moonrepo.dev/proto) `.prototools` files:

| Tool | Datasource | Package | Notes |
| --- | --- | --- | --- |
| `bun` | `github-releases` | `oven-sh/bun` | Grouped with `@types/bun` |
| `node` | `node-version` | `node` | Uses `node` versioning (handles loose versions like `"24"`) |
| `npm` | `npm` | `npm` | Standard npm registry |
| `gh` | `github-releases` | `cli/cli` | GitHub CLI |

Non-major updates for `node`, `npm`, and `gh` are grouped as **prototools toolchain**.

## Overriding for a specific repo

Add `packageRules` or `ignorePresets` in the repo's own `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>rhuanbarreto/renovate-config"],
  "packageRules": [
    {
      "description": "Repo-specific rule",
      "matchPackageNames": ["some-package"],
      "automerge": false
    }
  ]
}
```

## Presets reference

| Preset | Purpose |
| --- | --- |
| `config:best-practices` | Recommended + security defaults |
| `security:openssf-scorecard` | Supply-chain scorecard badges |
| `:approveMajorUpdates` | Dashboard gate for major bumps |
| `:enableVulnerabilityAlerts` | Vuln alert PRs |
| `:automergeRequireAllStatusChecks` | All CI must pass before automerge |
| `:rebaseStalePrs` | Keep PRs up to date with base |
| `schedule:nonOfficeHours` | Create PRs outside office hours |

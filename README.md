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
- **Vulnerability PRs** skip both the release-age delay **and** the monthly schedule (`schedule: ["at any time"]`), and get priority `10`
- **GitHub Actions pinned to SHA digests**
- **Major updates require dashboard approval** before PRs are created

### Noise reduction

- **Non-major devDependencies grouped** into a single PR per repo
- **Docs dependencies grouped** into a single PR
- **GitHub Actions grouped** into a single PR
- **Linters, formatters, and type packages auto-merged** on minor/patch
- **Schedule:** PRs created **only on the last Sunday of the month** (Sunday between the 25th and 31st, before 6am Europe/Oslo)
- **Automerges** are batched into the same monthly window
- **Rate limited:** max 20 concurrent PRs, no per-hour cap (so the full monthly batch can land in one run)
- **`internalChecksFilter: "strict"`** — only release PRs that pass all internal checks (e.g., release-age threshold)

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
| `:timezone(Europe/Oslo)` | Schedule timezone |

To opt out of the monthly schedule for a specific repo, override `schedule` and `automergeSchedule` in the repo config.

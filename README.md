# renovate-config

Shared [Renovate](https://docs.renovatebot.com) preset for first-it-consulting repositories.

## Usage

In a repository's `renovate.json`:

```json
{
  "extends": ["github>first-it-consulting/renovate-config"]
}
```

## What the preset does

- Base: `config:recommended`; timezone Europe/Berlin; updates scheduled on weekends (Sat/Sun 09:00-18:59).
- Rate limits: max 2 PRs/hour, max 5 open PRs; Dependency Dashboard enabled; PRs labeled `dependencies`.
- minor+patch updates are grouped per ecosystem (one PR per manager); docker and github-actions groups also cover digest/pinDigest bumps.
- github-actions action refs are pinned to commit digests.
- Automerge (minor/patch/digest, platform-native merge queue) is enabled ONLY for repositories listed in the automerge packageRule - these have required status checks on their default branch; extend that list as branch protection rolls out. Major updates are never automerged.

Config errors surface as issues via the Renovate Dependency Dashboard in each consuming repository.

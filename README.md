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
- Automerge (minor/patch only, platform-native merge queue) is enabled ONLY for repositories listed in the automerge packageRule - these have required status checks on their default branch; extend that list as branch protection rolls out. Digest/pinDigest bumps are grouped but never automerged. Major updates are never automerged.
- The automerge matchRepositories list is security-critical: only repos with required status checks; review on every change.

Config errors surface as issues via the Renovate Dependency Dashboard in each consuming repository.

## Rollout state (2026-11-26)

Mend app: ACTIVE. The GitHub installation was never inactive - the portal had Silent mode ON and Automated PRs OFF; both were corrected 2026-11-25 ~00:37 CET. Every repo with a renovate.json below now has a Dependency Dashboard from renovate[bot] on the Mend-hosted engine.

Portal settings (developer.mend.io): Silent mode OFF, Automated PRs ON, Require config file ON, Onboarding PRs OFF. Config-less repos are skipped and settle as `disabled`.

Gotchas (two separate causes, both seen in 2026-11):
- Silent mode (portal) = dry-run: jobs run and logs fill up but nothing is delivered. This explains why NO Renovate PRs appeared anywhere from 2026-01 until the 2026-11-25 settings fix.
- After the fix, "only hlw got a Dashboard" had a different cause: the free plan's scheduled queue (1 concurrent job per account) can stall for 30h+ with jobs stuck PENDING. Fix: portal -> repo -> Actions -> "Run Renovate scan" runs immediately; the remaining wave-1 Dashboards appeared this way.

Consumers (extend this preset, renovate.json on main):
hlw, llm-gateway-proxy, mysql-galera, fritzbox-skill, duckophila, weather-mcp-server, docker-reg-tool, helm-mcpo, playwright-skill, harness-control-center, claude-bridge, claude-memory (private), claude-memory-ui (private).
- claude-memory and claude-memory-ui keep their own packageRules on top of the preset, incl. automerge for minor/patch only (automergeType pr, checks-gated); they are NOT on the preset's platformAutomerge whitelist.
- harness-control-center and claude-bridge migrated off Dependabot (their dependabot.yml deleted in the same PR); claude-bridge also lost its dead dependabot-auto-merge.yml.

Not using the preset:
- containers - own .renovaterc.json5 (per-app docker-bake layout).
- apps - fork of truenas/apps, keeps its own self-hosted renovate.

Excluded on purpose (no Renovate):
- homebrew-tap - tap formulae are hand-bumped against upstream releases.
- open-webui-pr - fork; updates arrive via upstream syncs.
- docker_compose-harbor - harbor decommission in progress.
- blend_images_pipeline, blend_faces_pipeline - near-zero-churn utility scripts.
- microduck - fork; deps come from upstream.
- convert-xlsx-to-pdf, artifactory-client, build-pipeline-plugin - archived.
- coder-templates - private, dormant (no push since 2025-11); stays on Dependabot, out of the public-repo epic scope.

CI: .github/workflows/validate.yaml runs renovate-config-validator --no-global --strict on node >= 24 (renovate 44.x declares engines ^24.11.0 || ^26.13.0; on node 22 npx silently falls back to 42.99.0, so the workflow prints renovate --version as evidence it validates against Mend's engine).

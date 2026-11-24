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

## Rollout state (2026-11-24)

Consumers (extend this preset, renovate.json on main):
hlw, llm-gateway-proxy, mysql-galera, fritzbox-skill, duckophila, weather-mcp-server, docker-reg-tool, helm-mcpo, playwright-skill.

Excluded on purpose (no Renovate):
- homebrew-tap - tap formulae are hand-bumped against upstream releases.
- apps - fork of truenas/apps, keeps its own self-hosted renovate.
- open-webui-pr - fork; updates arrive via upstream syncs.
- docker_compose-harbor - harbor decommission in progress.
- blend_images_pipeline, blend_faces_pipeline - near-zero-churn utility scripts.
- microduck - fork; deps come from upstream.
- convert-xlsx-to-pdf, artifactory-client, build-pipeline-plugin - archived.

Pending migration:
- claude-memory, claude-memory-ui - self-hosted renovate.yml + RENOVATE_TOKEN PAT, to be retired and switched here (separate ticket).
- harness-control-center, claude-bridge - dependabot today, migrate via q59v.

STATUS: BLOCKED - the Mend Renovate app installation has been inactive account-wide since 2026-01 (no renovate[bot] activity anywhere; last bot PRs merged 2026-01-19). All configs above go live automatically once the installation is re-activated at github.com/settings/installations.

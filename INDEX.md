# Index

## Start here

- Repository purpose: `README.md`
- Stable operating rules: `AGENTS.md`
- Project-scoped Codex settings: `.codex/config.toml`
- Official source routing: `sources/official-sources.md`
- Environment topology: `docs/environment-topology.md`

## For Codex troubleshooting

- Main playbook: `docs/codex-support-playbook.md`
- Windows and desktop notes: `runbooks/codex-windows-notes.md`
- Codex troubleshooting runbook: `runbooks/codex-troubleshooting.md`

## For OpenClaw + Codex together

- Delta update: `docs/openclaw-codex-delta-update.md`
- Handoff runbook: `runbooks/openclaw-codex-handoff.md`
- Archive pointer only: `openclaw/README.md`
- Local environment baseline: `docs/environment-topology.md`

## For durable memory and preventing drift

- Plan template: `templates/PLAN_TEMPLATE.md`
- Incident template: `templates/INCIDENT_TEMPLATE.md`
- Implementation template: `templates/IMPLEMENTATION_RECORD_TEMPLATE.md`
- Change record template: `templates/CHANGE_RECORD_TEMPLATE.md`
- Existing records live under: `records/`

## For reusable workflows

- Combined-stack triage skill: `skills/combined-stack-triage/SKILL.md`
- Durable memory recording skill: `skills/durable-memory-recording/SKILL.md`

## Rules of thumb

- Use official docs first.
- Do not duplicate the OpenClaw archive.
- Keep anything that must always apply in Git, not only in memory.
- Before risky changes, write rollback steps into the active plan or implementation record.

## State snapshot areas
- `state-snapshots/codex/README.md`
- `state-snapshots/openclaw/README.md`

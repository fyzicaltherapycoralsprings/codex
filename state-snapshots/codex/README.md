# Codex State Snapshots

Use this area for **redacted operational snapshots** of the Codex environment.

What belongs here:
- project `AGENTS.md` snapshots when rules materially change
- `.codex/config.toml` snapshots
- redacted user/shared Codex config notes
- MCP server definitions with secrets removed
- approval / sandbox expectations
- worktree conventions
- durable memory/export notes
- known-good environment notes for MSI

What usually does **not** belong here:
- full binary backups
- caches
- raw local secrets
- tokens
- machine-wide dumps that cannot be reviewed in Git

Recommended subfolders:
- `config/`
- `mcp/`
- `workflows/`
- `memories/`
- `incidents/`

Suggested filename pattern:
`YYYY-MM-DD-topic.md`

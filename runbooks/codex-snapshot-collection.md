# Codex Snapshot Collection Runbook

Goal: collect a **redacted operational snapshot** that is useful for troubleshooting.

## Capture
- current project `AGENTS.md`
- project `.codex/config.toml`
- notes on user/shared config layers
- MCP server list and which ones are enabled
- approval mode and sandbox mode
- worktree usage notes
- notable memory/instruction behavior
- known-good repo layout for MSI

## Store
Write the snapshot into:
- `state-snapshots/codex/config/`
- `state-snapshots/codex/mcp/`
- `state-snapshots/codex/workflows/`
- `state-snapshots/codex/memories/`

## Redact
Remove:
- tokens
- auth headers
- local absolute paths that reveal secrets
- machine-specific private identifiers unless needed

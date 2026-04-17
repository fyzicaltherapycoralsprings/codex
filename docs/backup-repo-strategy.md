# Backup Repo Strategy

## Recommended model

Use three layers:

1. **Official sources**
   - OpenAI Codex docs
   - OpenClaw archive + docs.openclaw.ai

2. **Operating repo** (this repository)
   - instructions
   - runbooks
   - source routing
   - durable records
   - redacted state snapshots

3. **Full system backup**
   - only for rare forensics or disaster recovery
   - not the primary place to troubleshoot from

## Why this works
A full system backup is valuable, but a curated text-first repo is faster for retrieval, easier to diff, safer to share, and better for repeatable troubleshooting.

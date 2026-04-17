# Codex Windows Notes

This file exists because the active environment uses the desktop Codex client on Windows.

## Practical notes

- Keep project rules in Git so Windows-specific environment differences do not become invisible tribal knowledge.
- When app and terminal behavior differ, note whether the terminal is PowerShell, WSL, or another shell.
- Keep worktree setup steps explicit. Windows paths, local dependencies, and shell differences are common sources of silent failure.
- For repo-specific setup, prefer documented steps over local machine assumptions.

## What to document when Windows issues occur

- shell used
- whether the repo lived on Windows or in WSL
- exact failing command
- path format involved
- whether the issue reproduced in a fresh worktree
- whether the fix belongs in:
  - a runbook
  - `AGENTS.md`
  - `.codex/config.toml`
  - a future local-environment setup script

## Recommended habit

Any Windows-specific fix that works twice should be promoted into a checked-in record or runbook so it becomes durable project memory.


## Local host baseline for this repo

- Primary workstation: **MSI**
- Primary combined-stack role: OpenClaw + Codex host
- Tailscale node seen in admin screenshot: **`msi-old`**
- Tailscale IP seen in admin screenshot: **100.104.241.118**

Before writing machine-specific runbooks, confirm whether a note should refer to the Windows host name, the Tailscale node label, or both.

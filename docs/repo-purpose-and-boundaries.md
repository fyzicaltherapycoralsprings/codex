# Repo Purpose and Boundaries

This repository exists to make a combined **Codex + OpenClaw** setup easier to operate without duplicating official documentation.

## What this repo should contain
- stable instructions
- durable records
- environment topology
- runbooks
- source routing
- redacted state snapshots
- rollback notes

## What this repo should not contain
- copied official docs at large scale
- secrets or credentials
- raw support-pack tokens
- giant binary dumps unless there is a special forensic reason
- stale assumptions presented as fact

## Current environment bias
- MSI is the default combined OpenClaw + Codex host
- `clinic-server` is secondary and should not be pulled into every workflow
- verify whether the relevant Tailscale label is `msi-old` or `old-msi` before writing permanent host-specific guidance

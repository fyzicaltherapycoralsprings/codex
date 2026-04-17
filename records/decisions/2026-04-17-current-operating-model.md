# Decision Record: Current Operating Model

## Date
2026-04-17

## Decision
Treat **MSI** as the default combined **OpenClaw + Codex** host for most troubleshooting and operations.

Treat `clinic-server` as **secondary scope**, not a default participant in every investigation.

## Why
- Most current work is expected to happen on MSI.
- Codex and OpenClaw are both installed on MSI.
- Pulling `clinic-server` into every investigation adds noise and slows triage.

## Operational effect
- Start most combined-stack troubleshooting on MSI first.
- Only include `clinic-server` when the issue is clearly node-related, remote, Tailscale-related, or specific to that machine.

## Validation / notes
Verify the exact Tailscale machine label for MSI before writing permanent host-specific instructions if `old-msi` and `msi-old` both appear in notes or screenshots.

# Decision Record: Primary Combined-Stack Host Baseline

- Date: 2026-04-17
- Status: active
- Scope: Codex + OpenClaw operating repo

## Decision

Treat the **MSI workstation** as the default machine for combined OpenClaw + Codex work.

Do **not** automatically include `clinic-server` in every troubleshooting or planning flow.

## Why

The active operating model has changed:

- Codex is now being used as the “brain”
- OpenClaw remains the stronger tooling layer
- most work will happen on the MSI workstation
- `clinic-server` is no longer the default focus for every issue

## Current machine inventory

- `clinic-server` — `100.69.56.78`
- `msi` — `100.110.169.62`
- `msi-old` — `100.104.241.118`
- `s25` — `100.88.76.76`

## Naming caution

The user described the primary workstation’s Tailscale identity as **old-msi**, while the Tailscale admin screenshot shows **`msi-old`**.

Until that is reconciled, records should note both the human machine name (**MSI**) and the observed Tailscale node label.

## Operational effect

When starting a new incident or plan:

1. assume MSI workstation first
2. bring in `clinic-server` only if the issue points there
3. preserve both host role and Tailscale identity in the written record

## Rollback

If the environment changes again, update:

- `docs/environment-topology.md`
- `AGENTS.md`
- this decision record

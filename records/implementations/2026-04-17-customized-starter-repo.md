# Implementation Record: Customized Starter Repo for Local Topology

- Date: 2026-04-17
- Status: implemented

## Customization name / feature

Environment-aware Codex operations repo customized for the current OpenClaw + Codex setup.

## Steps / configuration

1. Added `docs/environment-topology.md`
2. Updated `AGENTS.md` with current machine-role defaults
3. Updated handoff and Windows runbooks to start on MSI by default
4. Updated the combined-stack triage skill to avoid automatically including `clinic-server`
5. Added a decision record for the primary-host baseline

## File references

- `docs/environment-topology.md`
- `AGENTS.md`
- `runbooks/openclaw-codex-handoff.md`
- `runbooks/codex-windows-notes.md`
- `skills/combined-stack-triage/SKILL.md`
- `records/decisions/2026-04-17-primary-host-baseline.md`

## Purpose / outcome

This customization makes the repo reflect the current operating reality:

- Codex + OpenClaw work centers on the MSI workstation
- `clinic-server` is secondary, not default
- Tailscale machine identities and addresses are written down so they do not drift out of memory

## Rollback steps

1. Remove or rewrite `docs/environment-topology.md`
2. Remove the environment-specific sections from `AGENTS.md`
3. Restore the handoff and skill files to a machine-agnostic version
4. Update or remove the decision record if the topology changes again

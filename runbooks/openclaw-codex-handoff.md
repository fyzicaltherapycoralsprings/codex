# OpenClaw ↔ Codex Handoff Runbook

Use this when the symptom may span both systems.

## Environment default

Start on the **MSI workstation** first, because that is the default OpenClaw + Codex host in this setup.

Do **not** automatically include `clinic-server` in every handoff investigation. Only include it when the symptom is clearly remote, node-related, or specific to that machine.

See `docs/environment-topology.md` for the current machine inventory and Tailscale addresses.

## Ownership test

### Usually OpenClaw-owned
- gateway not reachable
- channel probe failures
- node auth or pairing failures
- tool execution path failures
- config validation failures
- transport / network problems

### Usually Codex-owned
- wrong repo context
- wrong or missing durable instructions
- memory / instruction drift
- worktree-specific failures
- approvals / sandbox / MCP issues

### Usually handoff-owned
- Codex plans correctly but the OpenClaw tool call fails
- OpenClaw is healthy but Codex is using stale assumptions
- the right tool exists but is not being chosen
- the workflow depends on local repo files that OpenClaw never sees

## Safe routing sequence

1. Decide which machine owns the symptom first, starting with the MSI workstation by default.
2. Then decide whether the issue is OpenClaw, Codex, or handoff.
3. For OpenClaw-owned symptoms, use the archive and current docs rather than recreating docs here.
4. For Codex-owned symptoms, use `runbooks/codex-troubleshooting.md`.
5. For handoff-owned symptoms, write down:
   - what context Codex had
   - what tool or action OpenClaw attempted
   - what failed at the boundary
6. Record the fix in a durable file.

## OpenClaw command ladder reminder

When the OpenClaw side is suspected, start with the current command ladder from the OpenClaw docs rather than guessing.

## After a successful handoff fix

Update:
- `records/incidents/` if it was a break/fix
- `records/implementations/` if it changed operating behavior
- `AGENTS.md` if a stable new rule was learned

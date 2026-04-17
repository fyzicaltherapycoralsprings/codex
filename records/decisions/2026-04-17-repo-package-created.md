# Decision Record — Ready-to-Upload GitHub Package Created

## Decision
Create a GitHub-ready repository package that combines:
- Codex operating guidance
- OpenClaw handoff guidance
- environment topology
- redacted state-snapshot areas
- durable record templates

## Why
The user wanted a folder that could be uploaded directly to GitHub with the structure already in place.

## Environment assumptions captured
- MSI workstation is the default combined OpenClaw + Codex host
- `clinic-server` is secondary and not part of every workflow
- known Tailscale machine inventory currently includes:
  - `clinic-server` — `100.69.56.78`
  - `msi` — `100.110.169.62`
  - `msi-old` — `100.104.241.118`
  - `s25` — `100.88.76.76`

## Follow-up
Verify whether `msi-old` and `old-msi` refer to the same primary workstation label before locking host-specific instructions permanently.

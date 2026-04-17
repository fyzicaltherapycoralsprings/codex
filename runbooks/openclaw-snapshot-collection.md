# OpenClaw Snapshot Collection Runbook

Goal: collect a **redacted baseline** without replacing the live Support Pack as the current source of truth.

## Capture
- `openclaw status`
- `openclaw health`
- `openclaw gateway status`
- relevant `openclaw logs`
- config keys that changed
- node inventory and pairing notes
- support-pack summary and timestamp

## Store
Write the snapshot into:
- `state-snapshots/openclaw/status/`
- `state-snapshots/openclaw/config/`
- `state-snapshots/openclaw/nodes/`
- `state-snapshots/openclaw/support-pack/`

## Redact
Remove:
- bearer tokens
- auth-profile secrets
- raw credentials
- anything that would enable direct access if leaked

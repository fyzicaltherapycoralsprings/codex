# OpenClaw State Snapshots

Use this area for **redacted, text-first snapshots** of the OpenClaw environment.

Important:
- the live Support Pack remains the source of truth for the **current** deployment state
- this repo is for baselines, history, replication, and rollback support

What belongs here:
- config snapshots with secrets removed
- `openclaw status` captures
- `openclaw health` captures
- gateway status captures
- schema exports / config validation notes
- node inventory and pairing notes
- support-pack summaries or selected redacted artifacts
- implementation notes and rollback steps

Recommended subfolders:
- `config/`
- `status/`
- `support-pack/`
- `nodes/`
- `incidents/`

Suggested filename pattern:
`YYYY-MM-DD-topic.md`

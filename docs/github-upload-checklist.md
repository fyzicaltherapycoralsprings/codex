# GitHub Upload Checklist

Before first upload:
1. Confirm there are no secrets, tokens, or private keys in the repo.
2. Review `AGENTS.md`.
3. Review `.codex/config.toml`.
4. Review `docs/environment-topology.md`.
5. Decide whether to keep `clinic-server` references as secondary only.
6. Fill in at least one baseline under:
   - `state-snapshots/codex/config/`
   - `state-snapshots/openclaw/status/`

After upload:
1. Enable branch protection if desired.
2. Use issues or records for each real change.
3. Keep `sources/official-sources.md` current.
4. Keep `records/` updated after successful fixes.

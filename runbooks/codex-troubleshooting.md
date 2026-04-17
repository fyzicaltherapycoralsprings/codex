# Codex Troubleshooting Runbook

Use this when the issue appears to be primarily on the Codex side.

## Triage buckets

### Usually Codex-owned
- Wrong repo context
- Instructions in `AGENTS.md` are missing or contradictory
- Worktree setup failures
- Approval or sandbox blocks
- MCP server connection problems
- Memory not reflecting stable project conventions
- CLI / app auth issues
- Desktop app vs CLI behavior mismatch

### Fast checks

1. Confirm which Codex surface is failing:
   - app
   - CLI
   - CLI non-interactive (`codex exec`)
   - MCP client/server mode
2. Confirm whether the problem is:
   - configuration
   - environment
   - permissions / approvals
   - memory / instruction drift
   - worktree setup
3. Route to official sources from `sources/official-sources.md` before assuming behavior.

## Minimal evidence to collect

- exact surface involved
- exact command or action attempted
- full error text
- whether the issue reproduces in a clean worktree
- whether the same task fails in app, CLI, or both
- whether the project is trusted
- whether the relevant instruction exists in `AGENTS.md`
- whether the behavior depends on memory rather than checked-in docs

## Safe sequence

1. Reproduce in the smallest possible repo scope.
2. Check `AGENTS.md` and `.codex/config.toml` for conflicts.
3. If the issue involves worktrees, confirm the setup assumptions and any project-local bootstrapping.
4. If the issue involves approvals or network, identify whether sandbox or approval policy is blocking the action.
5. If the issue involves repeatable context loss, write the missing rule into Git instead of relying on memory.
6. Record the result under `records/`.

## Common patterns

### Memory drift
Prefer:
- `AGENTS.md`
- runbooks
- records
- skills

Do not rely on memory alone for critical operating rules.

### Worktree-only breakage
Suspect:
- missing setup script
- dependencies not installed in the worktree
- files that exist locally but are not checked in
- environment assumptions that were never documented

### CLI vs app mismatch
Check:
- whether the same repo path is in use
- whether project config is being loaded
- whether auth or trust differs
- whether a CLI flag or profile is overriding project defaults

### MCP issues
Check:
- whether the server definition is correct
- whether the transport is stdio or HTTP
- whether OAuth or bearer-token requirements apply
- whether the failure is authentication, transport, or tool semantics

## What to write after the fix

Create or update:
- a record under `records/`
- a runbook if the issue is likely to recur
- a skill if the workflow is reusable

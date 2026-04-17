# OpenClaw + Codex Delta Update
_Supplement to the existing OpenClaw archive. Last verified on 2026-04-17._

## Purpose
This file is **not** a replacement for the current OpenClaw archive. It is only the **delta layer** needed now that Codex is part of the stack.

Use the existing OpenClaw archive for:
- channels
- gateway operations
- configuration
- health
- logging
- pairing
- Tailscale / remote access
- protocol and tool behavior

Use this attachment for:
- how to split troubleshooting between OpenClaw and Codex
- a few current OpenClaw facts worth updating
- combined-stack rollback and triage rules

---

## 1) Keep the existing OpenClaw source hierarchy

The uploaded source pointer already identifies the active OpenClaw archive repo and says to start at `INDEX.md`, using canonical online links when available.

OpenClaw lookup order should remain:

1. Archive repo `INDEX.md` to locate the correct topic PDF / online article
2. `docs.openclaw.ai` for canonical current product behavior and supported config keys
3. `github.com/openclaw/openclaw` for implementation detail only

Do **not** duplicate the archive into future notes. Extend it only where new combined-stack behavior matters.

---

## 2) Combined operating model

### OpenClaw side
OpenClaw remains the tool-and-gateway side:
- gateway
- channels
- nodes
- tool routing / execution
- logs
- health
- config validation
- auth profiles
- paired-node connectivity

### Codex side
Codex is now the main “brain” layer for:
- durable instruction layering through `AGENTS.md`
- project-scoped behavior through `.codex/`
- local memory files under `~/.codex/memories/`
- coding / file reasoning in the desktop app and related Codex surfaces

### Combined-stack rule
When a symptom appears, determine whether the failure is primarily:

- **OpenClaw execution / gateway**
- **Codex reasoning / memory / repo context**
- **handoff between them**

---

## 3) Fast routing: which side owns the problem?

### Usually OpenClaw-owned
- gateway unreachable
- channel disconnected
- node pairing failure
- logs show transport / provider / channel errors
- tool invocation fails
- Support Pack shows node / net / disk / crash-loop problems
- config edits stop gateway startup
- Control UI / remote gateway / Tailscale issues

### Usually Codex-owned
- instructions “forgotten”
- wrong repo assumptions
- thread / project confusion
- worktree behavior differs from local checkout
- MCP tools missing from Codex
- app thread missing / archived / filtered
- automation not behaving like the manual task
- inconsistent coding style or repeated drift across sessions

### Usually handoff / integration-owned
- OpenClaw can act, but reasoning / planning is wrong
- Codex understands the repo, but OpenClaw cannot execute the intended tool chain
- the same task works in Codex app but fails when operationalized through OpenClaw
- durable rules live only in one side and are missing from the other

---

## 4) Current OpenClaw facts worth updating

These are the main OpenClaw-side deltas worth carrying forward alongside the archive.

### Strict config validation
Current docs say OpenClaw only accepts configurations that fully match the schema. Unknown keys, malformed types, or invalid values can cause the Gateway to refuse to start.

**Implication:** when troubleshooting post-edit failures, validate against the current schema before assuming runtime bugs.

### Live schema export exists
Current docs say:
- `openclaw config schema` prints the live JSON Schema used for validation and Control UI

**Implication:** this is the safest way to confirm whether a config key is supported now.

### Gateway reload modes matter
Current docs list:
- `hybrid` (default)
- `hot`
- `restart`
- `off`

**Implication:** “I changed config but behavior didn’t change” may be reload-mode behavior rather than a broken feature.

### Health command behavior matters
Current docs say:
- `openclaw health` can return a fresh cached gateway snapshot
- `openclaw health --verbose` forces a live probe

**Implication:** use `--verbose` when cached health is not enough.

### Gateway status survives some config problems
Current docs say:
- `gateway status` remains available even when local CLI config is missing or invalid

**Implication:** use it for diagnostics before reaching for riskier recovery steps.

### Logging has two surfaces
Current docs distinguish:
- console output
- file logs (JSON lines)

They also note that the Control UI Logs tab tails the gateway file log.

**Implication:** always identify which log surface is being quoted before drawing conclusions.

---

## 5) Combined troubleshooting order

### Step 1: Establish whether OpenClaw is healthy
Start with the normal OpenClaw diagnostic sequence / Support Pack workflow.

At minimum, confirm:
- `openclaw status`
- `openclaw health --verbose`
- `openclaw logs --follow`
- relevant node / channel status
- current config diff if a recent change exists

### Step 2: Establish whether Codex is healthy in the same project scope
Confirm:
- which Codex surface is in use (app / CLI / cloud / IDE)
- correct project / repo path
- expected `AGENTS.md` is loaded
- expected `.codex/config.toml` is loaded
- memories are enabled if the workflow depends on them
- MCP / tool integrations are present if the task depends on them
- worktree vs local mode is the intended one

### Step 3: Identify where durable instructions live
Required rules should be duplicated into durable, inspectable places:
- OpenClaw: config / local docs / skills / custom files as appropriate
- Codex: `AGENTS.md`, checked-in docs, `.codex/`, and only secondarily memories

### Step 4: Change one side at a time
Do **not** change OpenClaw config and Codex config in the same pass unless there is a compelling reason.
Use a single-variable change approach:
1. snapshot current state
2. change one layer
3. re-test
4. keep or roll back

---

## 6) Cross-system memory / drift prevention rules

1. Keep must-always-apply workflow rules in checked-in docs or `AGENTS.md`.
2. Treat Codex memories as recall help, not as the sole storage for operational requirements.
3. If OpenClaw has a custom workflow that must never be forgotten, store it in a local file and mention it from Codex guidance.
4. If Codex keeps solving a task the wrong way, update `AGENTS.md`, not just the current chat.
5. When a combined workaround succeeds, document:
   - what changed
   - why it helped
   - where the durable source now lives
   - how to roll it back

---

## 7) Safe rollback plan before integrated changes

Before changing the combined stack, back up:

### OpenClaw
- `~/.openclaw/openclaw.json`
- `~/.openclaw/agents/main/agent/auth-profiles.json`
- any custom OpenClaw docs / helper scripts / skill folders
- recent relevant log excerpt and config diff

### Codex
- `~/.codex/config.toml`
- `~/.codex/AGENTS.md` or `AGENTS.override.md`
- repo `.codex/`
- repo `AGENTS.md`
- any custom agent definitions under `~/.codex/agents/` or `.codex/agents/`

### Change-control rule
When possible:
- export / copy first
- edit second
- verify immediately
- keep the pre-change copy nearby for rollback

---

## 8) Recommended future documentation split

Keep future attached docs in three buckets:

1. **OpenClaw archive / delta**
   - gateway, nodes, channels, tooling, config, recovery
2. **Codex support docs**
   - app, CLI, memory, AGENTS, MCP, worktrees, automations
3. **Bridge docs**
   - how OpenClaw hands work to Codex
   - where durable context lives
   - how to debug mismatches between “brain” and “tools”

That avoids duplicating the OpenClaw archive while keeping the new Codex layer explicit.

---

## 9) Official sources used for this delta

### Existing archive pointer
- `openclaw-docs-sources.md` (uploaded source pointer)
- Archive repo: `https://github.com/fyzicaltherapycoralsprings/clawbot-doc-archive`
- Start at: `INDEX.md`

### Current OpenClaw docs used here
- Configuration: `https://docs.openclaw.ai/gateway/configuration`
- Configuration Reference: `https://docs.openclaw.ai/gateway/configuration-reference`
- Health Checks: `https://docs.openclaw.ai/gateway/health`
- CLI gateway docs: `https://docs.openclaw.ai/cli/gateway`
- Logging overview: `https://docs.openclaw.ai/logging`
- Gateway logging: `https://docs.openclaw.ai/gateway/logging`
- Gateway runbook: `https://docs.openclaw.ai/gateway`

### Codex docs used here
See the companion file `codex-support-playbook-2026-04-17.md`.

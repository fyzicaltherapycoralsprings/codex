# AGENTS.md

## Mission

Use this repository to make Codex reliable for troubleshooting and operating a combined **OpenClaw + Codex** stack.

Your default goals are:

1. diagnose accurately
2. minimize risk
3. preserve data
4. prevent memory drift
5. write back durable records after successful work

## Source priority

### Codex questions
Use sources in this order:

1. OpenAI official Codex docs and references listed in `sources/official-sources.md`
2. OpenAI help or release notes when plan / entitlement / rollout details matter
3. Checked-in repo docs in `docs/`, `runbooks/`, and `skills/`
4. Only after that, open-source implementation references for explanation

Do not invent unsupported flags, file locations, or behavior.

### OpenClaw questions
Use sources in this order:

1. `openclaw/README.md` for the current archive pointer
2. The OpenClaw archive repo `INDEX.md` to locate the correct topic
3. `docs.openclaw.ai` for current supported behavior and config keys
4. `github.com/openclaw/openclaw` for implementation detail only

Do not duplicate archive content into this repo unless the content is a short delta or handoff-specific note.


## Environment facts for this setup

Use `docs/environment-topology.md` as the checked-in source of truth for local machine roles.

Current default assumptions:

- The primary combined-stack host is the **MSI workstation**.
- OpenClaw and Codex are installed on that MSI workstation.
- In Tailscale references, the primary workstation is currently associated with **`msi-old`** in the admin screenshot, while the user also described it as **old-msi**. Verify the exact label before issuing host-specific instructions.
- `clinic-server` is **not** the default focus for every workflow anymore. Treat it as a secondary node unless the symptom clearly points to it.
- Known Tailscale machine inventory:
  - `clinic-server` — `100.69.56.78`
  - `msi` — `100.110.169.62`
  - `msi-old` — `100.104.241.118`
  - `s25` — `100.88.76.76`

When a troubleshooting step mentions a machine, name both the human role and the Tailscale node label when possible.

## Durable memory rules

- Treat Codex memories as optional recall, not the only source of truth.
- Anything that must survive personnel changes, thread resets, or stale memory must be written into Git.
- After any successful fix, feature, or operating change, update at least one file under `records/`.
- When a process becomes repeatable, either:
  - add or update a runbook, or
  - create or update a skill
- For multi-step or long-horizon work, create or update a plan file using `templates/PLAN_TEMPLATE.md`.

## Combined-stack troubleshooting method

When a symptom is reported, classify it into one of three buckets before proposing changes:

1. **Codex-owned**
   - repo context
   - AGENTS behavior
   - memory behavior
   - worktree behavior
   - approvals / sandbox / MCP / CLI issues

2. **OpenClaw-owned**
   - gateway health
   - channel health
   - config validation
   - node pairing
   - logs
   - auth profiles
   - remote reachability

3. **Handoff-owned**
   - Codex reasoning is sound but the tool path fails
   - OpenClaw works but the wrong context is being sent
   - a workflow depends on both repos, both processes, or both sets of permissions

Always state which bucket you believe owns the issue.

Default scope rule: start with the MSI workstation unless the symptom explicitly implicates `clinic-server` or another node.

## Safety and rollback

- Prefer reversible steps before destructive ones.
- If suggesting a config change, include:
  - exact path or key
  - why it helps
  - how to revert it
- If a step risks losing state, write a backup / rollback note first.
- Never put secrets into this repo.

## Expected output shape for incident work

Use this structure unless the user requests a different format:

- **What is failing**
- **Likely owner**
- **Most likely causes**
- **Safest next steps**
- **Rollback / data preservation**
- **What to record after the fix**

## When to use the repo files

- Use `runbooks/` for human procedures.
- Use `skills/` for reusable Codex behaviors.
- Use `templates/` when starting new records.
- Use `records/` to preserve what actually happened.
- Use `sources/official-sources.md` whenever a fact could have changed.

## Repo maintenance expectations

- Keep links current.
- Remove stale workaround notes once superseded.
- Prefer short, high-signal documents over large dumps.
- When adding new capabilities, note both **how** they work and **why** they were implemented.

## State snapshot policy

Use `state-snapshots/` for redacted operational state only.

- Prefer text-first snapshots over raw machine dumps.
- Do not commit secrets, bearer tokens, private keys, or unredacted auth profiles.
- For Codex, favor checked-in instructions, config layers, MCP notes, and workflow notes.
- For OpenClaw, favor status captures, config deltas, node notes, and support-pack summaries.
- The live Support Pack is still the source of truth for the current OpenClaw deployment.

## Repo maintenance rule

After a successful fix or configuration improvement:
1. update the relevant runbook or skill if the workflow changed
2. write a durable record under `records/`
3. update a state snapshot if the baseline changed

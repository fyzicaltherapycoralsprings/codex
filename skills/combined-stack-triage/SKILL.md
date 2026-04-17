---
name: combined-stack-triage
description: Use when a problem might involve both OpenClaw and Codex. Classify the issue as Codex-owned, OpenClaw-owned, or handoff-owned before proposing fixes, then prefer official docs and lowest-risk reversible steps.
---

# Combined Stack Triage

## Trigger

Use this skill when:
- the user mentions both OpenClaw and Codex
- the user is unsure which system owns the failure
- the symptom may involve repo context, memory, tooling, gateway health, or handoff behavior

Do not use this skill for purely local coding tasks that do not touch the combined stack.

## Procedure

1. Identify the failing machine first:
   - MSI workstation by default
   - `clinic-server` only when explicitly implicated
   - another Tailscale node if the symptom is network- or device-specific

2. Identify the failing surface:
   - Codex app
   - Codex CLI
   - OpenClaw gateway / nodes / channels
   - handoff between them

3. Classify ownership:
   - Codex-owned
   - OpenClaw-owned
   - handoff-owned

4. Use official source routing:
   - Codex: `sources/official-sources.md`
   - OpenClaw: `openclaw/README.md` and the archive pointer

5. Prefer reversible steps:
   - read-only checks first
   - then minimal config or workflow changes
   - then anything riskier only with rollback

6. Produce output in this shape:
   - What is failing
   - Likely owner
   - Most likely causes
   - Safest next steps
   - Rollback / data preservation
   - What to record after the fix

## Required guardrails

- Do not guess unsupported config keys or commands.
- Do not pull `clinic-server` into scope unless the symptom justifies it.
- Do not duplicate the OpenClaw archive.
- If a successful fix becomes recurring knowledge, add a record under `records/`.

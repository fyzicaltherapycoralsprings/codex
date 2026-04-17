---
name: durable-memory-recording
description: Use after a successful fix, customization, or recovery step to write the durable record into Git so the knowledge does not depend on Codex memory alone.
---

# Durable Memory Recording

## Trigger

Use this skill after:
- a successful troubleshooting fix
- a new operating convention
- a new integration or handoff rule
- a rollback plan that should be reused later

## Procedure

1. Decide which record type fits:
   - incident
   - implementation
   - decision
   - change record
   - plan

2. Start from the matching file in `templates/`.

3. Capture:
   - what changed
   - why it changed
   - exact commands or config keys
   - rollback steps
   - validation used
   - links to supporting runbooks or docs

4. Promote recurring knowledge:
   - update `AGENTS.md` if it is a standing rule
   - update a runbook if it is a repeatable procedure
   - create or update a skill if the workflow should be reusable

## Required guardrails

- Never store secrets.
- Keep records concise but specific.
- Prefer exact paths, commands, and timestamps over vague summaries.

# Codex Operations Repo Starter

This repository is a **curated operating repo** for Codex and the OpenClaw↔Codex handoff layer.

It is **not** a mirror of the official docs. Its job is to make Codex more effective inside your environment by keeping stable instructions, reusable workflows, durable records, and source routing in Git.

## What this repo is for

- Give Codex stable project instructions through `AGENTS.md`
- Store shared project defaults in `.codex/config.toml`
- Add repeatable workflows through `skills/`
- Keep durable records so fixes and customizations do not drift out of memory
- Route OpenClaw questions back to your archive and canonical docs instead of duplicating them
- Capture rollback steps before risky changes
- Preserve environment facts like host roles, Tailscale labels, and machine addresses so they do not drift

## What this repo is not for

- Copying large amounts of official documentation
- Replacing the OpenClaw archive repo
- Treating Codex memories as the only source of truth
- Storing secrets, bearer tokens, API keys, or private credentials

## First steps

1. Put this repo in Git.
2. Read `AGENTS.md` and edit the project-specific sections.
3. Review `.codex/config.toml` and adjust the defaults for your tolerance around approvals and sandboxing.
4. Keep the source map in `sources/official-sources.md` current.
5. Review `docs/environment-topology.md` and keep the machine inventory current.
6. After each successful fix or customization, write a record under `records/` using the templates in `templates/`.

## Layout

- `AGENTS.md` — the main operating rules for Codex in this repo
- `.codex/config.toml` — project-scoped Codex defaults
- `docs/` — compact guidance docs, environment topology, and official source map
- `openclaw/` — OpenClaw pointer files and handoff notes only
- `runbooks/` — human-readable troubleshooting procedures
- `skills/` — reusable Codex workflows
- `templates/` — durable-memory templates for incidents, plans, and implementations
- `records/` — actual written history of fixes and decisions
- `sources/` — canonical source routing index

## Usage pattern

Use this repo alongside Codex app worktrees and Codex CLI sessions. Keep instructions in Git, use memories as a convenience layer, and write back successful changes so future runs do not have to rediscover them.

See `INDEX.md` for where to go by task.

## Ready-to-upload use

This folder is already arranged as a GitHub-ready repository.

Recommended upload flow:
1. Create a new empty GitHub repository.
2. Upload this folder as the initial commit.
3. Review `AGENTS.md`, `.codex/config.toml`, and `docs/environment-topology.md`.
4. Fill in the snapshot areas under `state-snapshots/` with redacted exports from your real systems.
5. After each important fix, write a durable record under `records/`.

## Included state-snapshot areas

- `state-snapshots/codex/` — redacted Codex operational state, config, MCP, and workflow notes
- `state-snapshots/openclaw/` — redacted OpenClaw config, status captures, support-pack snapshots, and node notes

These are **templates and holding areas**, not raw secret dumps.

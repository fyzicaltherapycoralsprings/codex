# Codex Support Playbook for OpenClaw Helper
_Last verified against official OpenAI docs on 2026-04-17._

## Purpose
This attachment is the Codex-side operating manual for this GPT. It is written to help troubleshoot the **Codex app**, **Codex CLI**, and **Codex configuration / memory layers** without guessing.

Use this as the Codex companion to the existing OpenClaw documentation set.

## Source-of-truth policy
For Codex questions, prefer official OpenAI sources in this order:

1. `developers.openai.com/codex/*` for product behavior, configuration, app features, approvals, memory, MCP, worktrees, and troubleshooting.
2. `help.openai.com/*` for ChatGPT-plan, workspace-permission, and release-note details.
3. OpenAI blog / release notes for newly shipped behavior.
4. Only after the above, use open-source implementation references when behavior needs extra explanation.

Do **not** infer unsupported flags, file paths, or app behavior. Verify the exact page first.

---

## 1) What Codex surfaces exist

### Codex app
- The Codex app is the desktop “command center” with **parallel threads**, **built-in worktree support**, **automations**, and **Git functionality**.
- It is officially available on **macOS and Windows**.
- On **Windows**, the app runs natively with **PowerShell + Windows sandbox**, and can also work with **WSL2** projects.

### Codex CLI
- The CLI is the terminal client (`codex`) for local repo work.
- Official install path:
  - `npm i -g @openai/codex`
- First run:
  - `codex`
- Script / CI mode:
  - `codex exec "..."`

### Important Windows distinction
- The **Codex app** is officially supported on Windows.
- The **Codex CLI** docs still say **Windows support is experimental** and recommend **WSL2** for the best Windows terminal experience.

### Shared behavior across surfaces
The official docs state that the CLI and IDE extension share configuration layers, and the app interoperates with the same Codex ecosystem. In practice, troubleshooting should always identify **which surface** is failing first:
- app
- CLI
- IDE extension
- Codex cloud / delegated cloud tasks

---

## 2) Where Codex stores durable state

Codex stores local state under `CODEX_HOME`, which defaults to:

- `~/.codex`

Common files / folders called out in official docs:
- `~/.codex/config.toml` — user-level Codex configuration
- `~/.codex/auth.json` — cached login details, when file-based credential storage is used
- `~/.codex/history.jsonl` — if history persistence is enabled
- `~/.codex/memories/` — Codex memory files
- `~/.codex/AGENTS.md` or `~/.codex/AGENTS.override.md` — global instruction layer
- `~/.codex/agents/` — custom personal agents

Project-scoped durable layers:
- `.codex/config.toml`
- `.codex/agents/`
- `.codex/` local-environment files created by the Codex app
- repo / subdirectory `AGENTS.md`

### Configuration precedence
Official precedence order:
1. CLI flags and `--config` overrides
2. selected profile
3. project `.codex/config.toml` layers from project root down to current directory (trusted projects only)
4. user config `~/.codex/config.toml`
5. system config (Unix, if present)
6. built-in defaults

This means “Codex ignored my config” often turns out to be:
- wrong working directory
- a more specific `.codex/config.toml`
- a selected profile
- project not marked trusted
- a one-off CLI override

---

## 3) Codex memory model: what to trust

### Memories
Official docs say memories can carry forward:
- stable preferences
- recurring workflows
- tech stacks
- project conventions
- known pitfalls

They are generated into local memory files under `~/.codex/memories/`.

### What memories are **not**
OpenAI explicitly says to keep required team guidance in:
- `AGENTS.md`, or
- checked-in documentation

Memories are a **helpful local recall layer**, not the only place for rules that must always apply.

### Practical rule for this GPT
When a user says Codex is “forgetting things,” check this order:
1. Is the missing guidance actually in `AGENTS.md` or only in memory?
2. Are memories enabled?
3. Has the thread been idle long enough for memory generation to occur?
4. Is Codex in the same project / repo scope as before?
5. Is there a more specific `AGENTS.md` overriding the expected instruction?

### Enabling memories
The official docs show:
```toml
[features]
memories = true
```

---

## 4) AGENTS.md: the most important durable instruction layer

Official docs say Codex reads `AGENTS.md` files **before doing any work**.

Discovery starts from:
1. global `~/.codex/AGENTS.override.md` if present, otherwise `~/.codex/AGENTS.md`
2. repo / subdirectory `AGENTS.md` layers closer to the current directory

Key operating rules:
- Put **must-always-apply** instructions here, not in temporary prompts.
- Keep it short and specific.
- When Codex repeats a mistake, update `AGENTS.md`.
- Put repository run/test/lint commands here.
- Put “done means…” here.
- Put safety / do-not-touch rules here.

Useful official detail:
- `/init` can scaffold a starter `AGENTS.md` in the current directory.
- More specific files closer to the working directory win.

### Recommended Codex attachment strategy
For long-term reliability:
- Global personal defaults: `~/.codex/AGENTS.md`
- Shared repo behavior: repo-root `AGENTS.md`
- Local exceptions: nested `AGENTS.md`
- Long-running specs / plans: checked-in markdown files referenced from `AGENTS.md`

---

## 5) Worktrees, local environments, and background automations

### Worktrees
In the app, worktrees let Codex run parallel tasks in the same Git repo without disturbing the local checkout.

Common failure mode:
- “The code runs locally but not on the worktree.”

Official docs explain why:
- worktrees are in a different directory
- they only inherit **checked-in** files
- missing dependencies / generated artifacts / setup steps can break them

### Local environments
The Codex app supports local environments for:
- setup scripts for new worktrees
- reusable project actions like build / run / test

The generated local-environment configuration lives inside the project’s `.codex` folder and can be committed.

### Automations
Automations:
- run recurring tasks in the background
- can use the same plugins and skills as Codex
- can run in local project or in a dedicated worktree
- require the **app to be running** and the selected project to be available on disk for project-scoped automations

Important risk rule:
- background automations inherit sandbox settings
- read-only mode blocks changes
- full-access background automations are higher risk

### Practical troubleshooting order for worktree / automation issues
1. Confirm the project is actually a Git repo.
2. Confirm the app is still running (for project-scoped automations).
3. Confirm setup scripts exist for worktrees if dependencies are not fully checked in.
4. Confirm the worktree branch / base branch is correct.
5. Confirm sandbox mode is not preventing the action.
6. Confirm the task should run in worktree vs local mode.

---

## 6) Authentication troubleshooting

Official Codex auth methods:
- **Sign in with ChatGPT**
- **Sign in with an API key**

Important official distinctions:
- **Codex cloud requires ChatGPT sign-in**
- CLI / IDE can use either ChatGPT or API key
- login details are cached locally and reused
- cached login may live in `~/.codex/auth.json` or the OS credential store

### When to suspect auth method mismatch
- Cloud task works / local does not, or vice versa
- expected ChatGPT-plan benefits are missing
- workspace admin controls are not applying as expected
- user expected subscription access but is actually billed via API key usage

### Safe auth checks
- Identify whether the failing surface is app / CLI / cloud / IDE
- Identify which sign-in method is in use
- Confirm whether the user expected ChatGPT-plan behavior or API billing behavior
- Check whether the cached session is stale
- Avoid exposing token contents in logs or screenshots

---

## 7) Sandbox and approvals troubleshooting

Official docs distinguish:
- **sandbox mode** = technical boundaries
- **approval policy** = when Codex must ask before acting

For local usage, sandbox problems can look like:
- Codex can read but not write
- Codex cannot access network
- command works manually but not through Codex
- background automation silently fails on actions requiring more permissions

For Windows app usage:
- the app can use the native Windows sandbox in PowerShell mode
- WSL2 projects use Linux-style sandboxing

### Default support posture
- Keep approvals and sandboxing tight by default
- loosen only for trusted repos or narrow workflows
- prefer targeted exceptions / rules over broad full-access mode

---

## 8) MCP troubleshooting

Official MCP facts:
- MCP config is stored in `config.toml`
- shared across Codex CLI and IDE extension
- can be managed with `codex mcp`
- active MCP servers can be viewed in TUI with `/mcp`

### MCP failure checklist
1. Is the MCP server defined in `~/.codex/config.toml` or a trusted project `.codex/config.toml`?
2. Is the project trusted?
3. Was the server added with the expected command / environment variables?
4. Does the MCP server require OAuth?
5. Is the same config visible across the relevant Codex surface?

---

## 9) App-specific troubleshooting cues

### Missing threads
Official app troubleshooting says:
- switch thread filter to **Chronological**
- then check archived chats / archived threads in Settings

### Project sidebar issues
Projects can be removed and re-added from the sidebar / Add new project flow.

### Code does not run on a worktree
Official guidance points back to:
- different directory
- only checked-in files copied
- setup scripts / local environment may be needed

### Windows project location
For WSL2 projects in the Windows app, the docs say you can open them through:
- `\\wsl$\`

---

## 10) Best-practice patterns this GPT should recommend for Codex

1. Put durable rules in `AGENTS.md`, not in ad hoc prompts.
2. Put long-horizon specs and validations in checked-in markdown files.
3. Use memories as a bonus, not as the only copy of critical instructions.
4. Use project `.codex/config.toml` for repo-scoped behavior; keep personal defaults in `~/.codex/config.toml`.
5. For repeated tasks, prefer skills or automations over repeated manual prompting.
6. For difficult tasks, ask Codex to plan first.
7. For parallel risky work, use worktrees.
8. For Windows terminal use, prefer WSL2 when the user is relying on CLI behavior rather than the desktop app.

---

## 11) Safe rollback / backup plan before Codex-side changes

Before changing Codex behavior in a meaningful way, back up:
- `~/.codex/config.toml`
- `~/.codex/AGENTS.md` / `AGENTS.override.md`
- repo `.codex/`
- repo `AGENTS.md`
- any custom agents under `~/.codex/agents/` or `.codex/agents/`

If troubleshooting memory behavior specifically, also preserve:
- `~/.codex/memories/` (copy only; do not expose private contents casually)

---

## 12) Official source map used for this document

### Core docs
- Codex overview: `https://developers.openai.com/codex`
- Codex app: `https://developers.openai.com/codex/app`
- Codex CLI: `https://developers.openai.com/codex/cli`
- Codex app troubleshooting: `https://developers.openai.com/codex/app/troubleshooting`
- Codex Windows app: `https://developers.openai.com/codex/app/windows`
- Config basics: `https://developers.openai.com/codex/config-basic`
- Advanced config: `https://developers.openai.com/codex/config-advanced`
- Config reference: `https://developers.openai.com/codex/config-reference`
- Authentication: `https://developers.openai.com/codex/auth`
- Agent approvals & security: `https://developers.openai.com/codex/agent-approvals-security`
- Sandbox concepts: `https://developers.openai.com/codex/concepts/sandboxing`
- AGENTS.md guide: `https://developers.openai.com/codex/guides/agents-md`
- Memories: `https://developers.openai.com/codex/memories`
- MCP: `https://developers.openai.com/codex/mcp`
- Worktrees: `https://developers.openai.com/codex/app/worktrees`
- Local environments: `https://developers.openai.com/codex/app/local-environments`
- Automations: `https://developers.openai.com/codex/app/automations`
- Best practices: `https://developers.openai.com/codex/learn/best-practices`
- Subagents: `https://developers.openai.com/codex/subagents`
- Non-interactive mode: `https://developers.openai.com/codex/noninteractive`

### ChatGPT plan / workspace docs
- Using Codex with your ChatGPT plan: `https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan`
- ChatGPT release notes (Windows app availability): `https://help.openai.com/en/articles/6825453-chatgpt-release-notes`

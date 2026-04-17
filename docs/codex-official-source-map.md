# Codex Official Source Map for Future Troubleshooting
_Last verified on 2026-04-17._

This is a compact “where to look first” index for Codex support work.

## Start here by problem type

### Product overview / what surface we are talking about
- Codex overview: `https://developers.openai.com/codex`
- Codex app: `https://developers.openai.com/codex/app`
- Codex CLI: `https://developers.openai.com/codex/cli`
- Codex web / cloud: `https://developers.openai.com/codex/cloud`
- Codex IDE extension: `https://developers.openai.com/codex/ide`

### Authentication / workspace controls / plan access
- Auth: `https://developers.openai.com/codex/auth`
- ChatGPT plan FAQ: `https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan`
- ChatGPT release notes: `https://help.openai.com/en/articles/6825453-chatgpt-release-notes`

### Config, state, and precedence
- Config basics: `https://developers.openai.com/codex/config-basic`
- Advanced config: `https://developers.openai.com/codex/config-advanced`
- Config reference: `https://developers.openai.com/codex/config-reference`
- Sample config: `https://developers.openai.com/codex/config-sample`

### Memory and durable instructions
- Memories: `https://developers.openai.com/codex/memories`
- AGENTS.md guide: `https://developers.openai.com/codex/guides/agents-md`
- Best practices: `https://developers.openai.com/codex/learn/best-practices`
- Customization concepts: `https://developers.openai.com/codex/concepts/customization`

### MCP / tools / integrations
- MCP: `https://developers.openai.com/codex/mcp`
- App settings (Integrations & MCP): `https://developers.openai.com/codex/app/settings`

### App-specific issues
- App troubleshooting: `https://developers.openai.com/codex/app/troubleshooting`
- Worktrees: `https://developers.openai.com/codex/app/worktrees`
- Local environments: `https://developers.openai.com/codex/app/local-environments`
- Automations: `https://developers.openai.com/codex/app/automations`
- Review pane: `https://developers.openai.com/codex/app/review`
- Windows app: `https://developers.openai.com/codex/app/windows`
- Remote connections: `https://developers.openai.com/codex/remote-connections`

### Execution / approvals / sandboxing
- Agent approvals & security: `https://developers.openai.com/codex/agent-approvals-security`
- Sandbox concepts: `https://developers.openai.com/codex/concepts/sandboxing`
- Non-interactive mode (`codex exec`): `https://developers.openai.com/codex/noninteractive`
- CLI reference: `https://developers.openai.com/codex/cli/reference`

### More advanced behavior
- Subagents: `https://developers.openai.com/codex/subagents`
- Skills: `https://developers.openai.com/codex/skills`
- Changelog: `https://developers.openai.com/codex/changelog`
- Run long-horizon tasks with Codex: `https://developers.openai.com/blog/run-long-horizon-tasks-with-codex`

## Quick lookup rules for this GPT

1. If the issue is about **Codex forgetting**, read:
   - Memories
   - AGENTS.md guide
   - Best practices

2. If the issue is about **wrong settings / inconsistent behavior**, read:
   - Config basics
   - Advanced config
   - Config reference

3. If the issue is about **worktrees / app behavior**, read:
   - Worktrees
   - Local environments
   - Automations
   - App troubleshooting
   - Windows app docs if relevant

4. If the issue is about **permissions / failed actions / network / file access**, read:
   - Agent approvals & security
   - Sandbox concepts

5. If the issue is about **external tools or shared integrations**, read:
   - MCP
   - App settings
   - relevant plugin / integration page

6. If the issue is about **CI, scripts, or deterministic runs**, read:
   - Non-interactive mode
   - CLI reference

## OpenClaw coexistence rule
This index is only for Codex. For OpenClaw, continue to start at the archive repo’s `INDEX.md` and use current `docs.openclaw.ai` pages as canonical when there is a conflict or when a config key must be confirmed live.

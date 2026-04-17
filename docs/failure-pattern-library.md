# Failure Pattern Library (Expanded Diagnostic Engine)

This file is intended to function as an **operator-grade diagnostic engine** for OpenClaw + Codex, not just a reference list.

It is designed to:
- reduce guesswork
- force evidence-backed diagnosis
- separate OpenClaw, Codex, Network, and Integration failures
- route toward the safest reversible step first
- preserve rollback discipline
- make recurring incidents easier to classify and document

This file should be consulted **before** forming new hypotheses when troubleshooting.

---

## Mandatory usage rules

1. **Classify the issue first**
   - Runtime failure
   - Configuration error
   - Authentication issue
   - Network issue
   - Instruction / behavior issue
   - Integration / handoff issue

2. **Isolate the primary layer first**
   - Codex layer
   - OpenClaw layer
   - Network layer
   - Cross-layer integration

3. **Match symptoms to candidate patterns**
   - do not jump to fixes before checking pattern fit
   - multiple patterns may partially match; rank them by evidence

4. **Verify required evidence**
   - if required evidence is missing, do not claim the pattern is confirmed
   - instead state what evidence is missing and collect it

5. **Prefer reversible actions first**
   - restart before reinstall
   - compare config before rewriting config
   - inspect docs/config before changing behavior rules
   - record rollback before applying non-trivial changes

6. **If no pattern matches**
   - state: `No matching failure pattern`
   - continue with the standard diagnostic workflow
   - document the new pattern later if confirmed

---

## Evidence confidence scale

Use this when applying any pattern.

### Confirmed
Use only when:
- required evidence is present, and
- the decision logic clearly fits observed behavior

### Strong candidate
Use when:
- most required evidence is present, and
- the symptom cluster fits, but
- one confirming signal is still missing

### Speculative
Use only when:
- symptom fit is plausible, but
- the required evidence is incomplete

If a cause is speculative, say so explicitly and prioritize data collection.

---

## Required evidence sources by layer

### OpenClaw
Use these first when available:
- `/support/meta`
- `/support/gateway-uptime`
- `/support/status`
- `/support/nodes`
- `/support/errors-summary`
- `/support/netcheck`
- `/support/log-tail`
- `/support/config`
- `/support/config-diff`
- `/support/disk`

### Codex
Use these first when available:
- `AGENTS.md`
- `.codex/config.toml`
- `docs/`
- `runbooks/`
- `skills/`
- `records/`
- `docs/failure-patterns.md`
- official Codex docs
- any relevant MCP config or tool config in the repo

### Network
Use these first when available:
- Support Pack `netcheck`
- connection-related log excerpts
- Tailscale role / topology docs
- funnel/serve status
- TLS/network errors in logs

---

# OPENCLAW LAYER

## Pattern: node_token_mismatch

**Layer:** OpenClaw  
**Classification:** Authentication issue

### Typical symptoms
- node shows as present but not healthy
- node repeatedly fails to authenticate or reconnect
- node pairing appears broken after a credential or token change
- logs/errors mention auth mismatch, invalid token, or repeated pairing failure

### Required evidence
- `/support/nodes`
- `/support/errors-summary`
- relevant `/support/log-tail` lines showing auth or pairing failure

### Evidence that strengthens confidence
- repeated auth failures for the same node
- one node unhealthy while gateway remains otherwise healthy
- recent re-pair / config change / token rotation in records

### Evidence that weakens confidence
- all nodes are failing equally
- there are broad connectivity failures
- errors point to TLS/network rather than auth

### Decision logic
- If a node is reachable/present but fails auth repeatedly, treat `token_mismatch` as a strong candidate.
- If the error stream clearly identifies token mismatch or invalid credentials, classify as confirmed.
- If the node is simply offline with no auth evidence, do not diagnose token mismatch yet.

### Most likely causes
1. outdated node token
2. node re-paired incompletely
3. wrong token copied to one side
4. stale config or stale auth state after change

### Safest next steps
1. capture current node status and relevant logs
2. confirm which node is affected
3. verify whether a recent token/pairing change occurred
4. re-pair the affected node or update the node token carefully
5. re-check node health once, without retry loops

### Rollback
- restore the previous token/pairing state if documented
- restore previous config snapshot if the change was made via config
- document exactly which node was modified

### What to document if fixed
- affected node name
- exact re-pair / token update steps
- where the authoritative token lives
- rollback path if this happens again

---

## Pattern: gateway_crash_loop

**Layer:** OpenClaw  
**Classification:** Runtime failure

### Typical symptoms
- gateway restarts repeatedly
- uptime remains low or resets often
- service appears alive intermittently but never stabilizes
- similar errors repeat in log tail

### Required evidence
- `/support/gateway-uptime`
- `/support/log-tail`
- `/support/errors-summary`
- `/support/config-diff`

### Evidence that strengthens confidence
- recurring restart pattern in uptime
- same stack trace or failure point appearing repeatedly
- recent config change before onset
- errors spike shortly after startup

### Evidence that weakens confidence
- gateway uptime is stable
- issue is isolated to one tool or one downstream service
- errors are user-action-specific rather than startup/systemic

### Decision logic
- If uptime repeatedly resets and logs show repeated failure on startup or shortly after, classify as a crash loop.
- If crash loop started after config change, prioritize config regression.
- If no repeated restart evidence exists, do not call it a crash loop.

### Most likely causes
1. invalid or destabilizing recent config change
2. dependency failure during startup
3. repeated provider/tool init failure
4. resource exhaustion causing repeated failure
5. corrupted local state requiring targeted cleanup

### Safest next steps
1. capture config diff and last 200+ relevant log lines
2. identify whether the failure started after a known config or environment change
3. revert the smallest recent change first
4. avoid reinstall/reset until logs and diff are preserved
5. restart once after rollback, then re-check uptime

### Rollback
- restore prior known-good config
- restore prior auth/provider state if that changed
- note which config key/file was reverted

### What to document if fixed
- triggering change
- exact failing component from logs
- exact rollback
- exact known-good state

---

## Pattern: resource_pressure_disk_or_memory

**Layer:** OpenClaw  
**Classification:** Runtime failure

### Typical symptoms
- degraded performance
- failures under load
- delayed responses
- log write failures or warnings
- instability after long uptime or growth in logs/state

### Required evidence
- `/support/disk`
- `/support/log-tail`
- `/support/errors-summary`

### Evidence that strengthens confidence
- low free disk
- memory/resource warnings in logs
- failures align with heavy activity or large logs
- recovery after freeing resources

### Evidence that weakens confidence
- healthy disk and no resource warnings
- failures are instant and deterministic after a specific config change
- issue isolated to one remote node/network path

### Decision logic
- If free disk is low or logs show memory/resource pressure, treat resource pressure as confirmed or strong candidate depending on severity.
- If no resource signals exist, do not use this as a generic explanation.

### Most likely causes
1. low disk space from logs, snapshots, or accumulated state
2. excessive concurrency or workload
3. runaway logging or repeated failures bloating state
4. background processes competing for resources

### Safest next steps
1. preserve current logs before cleanup
2. identify largest/fastest-growing files or directories
3. free space conservatively
4. reduce concurrency or noisy behavior if configured
5. re-check stability after one controlled change

### Rollback
- restore prior workload/concurrency settings if changed
- keep a note of deleted/rotated artifacts if cleanup was manual

### What to document if fixed
- threshold at which failures started
- directories/logs responsible
- preventative cleanup or retention settings
- workload settings changed

---

## Pattern: verbose_tool_spam

**Layer:** OpenClaw  
**Classification:** Configuration error / behavior issue

### Typical symptoms
- excessive tool bubbles or noisy status output
- user-facing tool chatter
- workflows become noisy even when functionality works

### Required evidence
- user report and/or screenshots
- relevant config showing verbose default behavior
- confirmation of current verbosity setting if available

### Evidence that strengthens confidence
- persistent excessive tool output across tasks
- verbose setting enabled by default
- issue resolves when verbosity is disabled

### Evidence that weakens confidence
- chatter is caused by repeated retries rather than verbosity
- noise is coming from another layer (e.g. Codex or UI workflow)

### Decision logic
- If tool chatter is persistent and verbosity/defaults are enabled, treat verbose/tool spam as confirmed.
- If the chatter is due to failure loops, diagnose the failure loop instead.

### Most likely causes
1. verbose mode enabled
2. overly chatty workflow design
3. repeated retries amplifying otherwise normal output

### Safest next steps
1. instruct `/verbose off` if appropriate in the relevant interface
2. verify `agents.defaults.verboseDefault` is off
3. reduce repeated polling or retry behavior

### Rollback
- re-enable verbosity if troubleshooting specifically requires it
- document prior setting

### What to document if fixed
- exact setting changed
- why it was noisy
- when verbosity should be temporarily re-enabled

---

## Pattern: provider_auth_misread

**Layer:** OpenClaw  
**Classification:** Authentication / configuration issue

### Typical symptoms
- provider/model behavior fails unexpectedly
- assumption that missing `.env` keys are the problem
- auth overview inconsistent with expected provider state

### Required evidence
- `openclaw status`
- auth overview
- configured models
- fallback models
- auth profiles file location knowledge

### Evidence that strengthens confidence
- auth profiles missing/incorrect
- status output inconsistent with intended provider config
- someone attempted to diagnose via `.env` instead of `auth-profiles.json`

### Evidence that weakens confidence
- status output shows auth healthy
- failures clearly tied to network or model provider outage instead

### Decision logic
- If provider diagnosis is being made from `.env` assumptions rather than `auth-profiles.json` and status, correct the diagnostic path first.
- If auth overview directly shows mismatch/problem, treat as confirmed.

### Most likely causes
1. wrong auth profile state
2. misread config location
3. fallback models not configured as expected
4. stale assumptions about how provider auth works

### Safest next steps
1. inspect `openclaw status`
2. inspect auth overview and configured models
3. inspect fallback model behavior
4. correct auth in `~\.openclaw\agents\main\agent\auth-profiles.json` if needed

### Rollback
- restore prior auth profiles backup if changed
- document exact profile changes

### What to document if fixed
- correct source of truth for auth
- exact profile keys changed
- fallback expectations

---

# NETWORK LAYER

## Pattern: tls_or_port_443_failure

**Layer:** Network  
**Classification:** Network issue

### Typical symptoms
- TLS handshake errors
- outbound connectivity failure
- Telegram or external provider instability
- failures to reach services over 443 while local state appears healthy

### Required evidence
- `/support/netcheck`
- relevant `/support/log-tail` lines
- `/support/errors-summary`

### Evidence that strengthens confidence
- clear TLS/443 errors in logs
- netcheck indicating outbound/connectivity trouble
- issue affects externally reached services rather than internal-only components

### Evidence that weakens confidence
- auth mismatch errors without network failures
- purely local config errors
- failures isolated to one internal file/config path

### Decision logic
- If TLS/443 failures appear in logs and netcheck supports connectivity trouble, classify as confirmed.
- If logs only show auth or config issues, do not attribute to TLS/network.

### Most likely causes
1. firewall blocking or inspection
2. router/ISP interference
3. local TLS/connectivity restrictions
4. transient upstream network path instability

### Safest next steps
1. preserve logs showing TLS failure
2. verify outbound 443 path
3. inspect firewall/network policy changes
4. avoid broad reconfiguration until connectivity evidence is clear

### Rollback
- revert firewall or network policy changes that were introduced
- document exactly what was changed

### What to document if fixed
- exact rule/device responsible
- exact corrective change
- any environment-specific networking caveats

---

## Pattern: support_pack_api_unreachable

**Layer:** Network / access path  
**Classification:** Network issue / access issue

### Typical symptoms
- Support Pack action calls fail with auth error, timeout, or unreachable status
- live deployment state cannot be retrieved through the normal action path

### Required evidence
- failed action call result
- any available funnel/serve status or access notes

### Evidence that strengthens confidence
- repeated action failures across endpoints
- 401/403/timeout patterns
- known funnel/token changes

### Evidence that weakens confidence
- action calls succeed
- failures limited to one endpoint with others healthy

### Decision logic
- If action calls fail for auth or timeout reasons, classify as `Support Pack API unreachable`.
- Do not guess live deployment state after this point.

### Most likely causes
1. funnel URL changed
2. token/header issue
3. `tailscale serve` not active or misconfigured
4. `tailscale funnel` not active or misconfigured

### Safest next steps
1. state explicitly: `Support Pack API unreachable`
2. check funnel URL
3. check token header
4. check `tailscale serve` status
5. check `tailscale funnel` status

### Rollback
- restore prior working funnel/token/serve configuration

### What to document if fixed
- authoritative funnel URL
- token/header location
- normal serve/funnel verification steps

---

# CODEX LAYER

## Pattern: instruction_drift_or_incomplete_guidance

**Layer:** Codex  
**Classification:** Instruction / behavior issue

### Typical symptoms
- behavior inconsistent with expected workflow
- assistant follows generic logic instead of repo-specific operating rules
- repeated need to restate stable rules
- instructions appear to exist but behavior does not fully align

### Required evidence
- `AGENTS.md`
- relevant task context
- comparison between expected behavior and current response pattern
- any folder-level AGENTS overrides if applicable

### Evidence that strengthens confidence
- instructions missing, ambiguous, or contradictory
- behavior improves after clarifying `AGENTS.md`
- task is in subdirectory with different instructions

### Evidence that weakens confidence
- config clearly controls the behavior instead
- tooling failure prevents instruction use
- expected rule only exists in memory and not in repo/docs

### Decision logic
- If expected workflow is not explicitly and clearly written, treat instruction drift/incomplete guidance as likely.
- If there are multiple `AGENTS.md` files, inspect hierarchy before diagnosing.
- If instructions are clear but execution settings differ, prefer config mismatch instead.

### Most likely causes
1. missing or underspecified `AGENTS.md` guidance
2. conflicting AGENTS hierarchy
3. stable rule living in memory instead of repo
4. repo docs missing current environment-specific behavior

### Safest next steps
1. inspect project and nearer-folder `AGENTS.md`
2. compare expected workflow to written instructions
3. add or tighten exact behavior rules in checked-in guidance
4. avoid relying on memory to compensate

### Rollback
- revert `AGENTS.md` edit if behavior worsens
- preserve diff of instruction changes

### What to document if fixed
- exact instruction added/clarified
- which file/path owns that rule
- why repo guidance was insufficient before

---

## Pattern: codex_config_precedence_mismatch

**Layer:** Codex  
**Classification:** Configuration error

### Typical symptoms
- unexpected project-specific behavior
- behavior differs between repos or subdirectories
- assistant seems to ignore expected repo instructions because execution settings differ
- tools, approvals, or behavior differ from what user expects

### Required evidence
- project `.codex/config.toml`
- relevant `AGENTS.md`
- any user/global config when needed
- observed behavior tied to config-governed areas

### Evidence that strengthens confidence
- project config explicitly differs from expected behavior
- project and global settings conflict
- issue disappears when correct project config is restored

### Evidence that weakens confidence
- behavior mismatch is purely about missing instructions
- tool failure is due to external outage rather than config
- no relevant config exists for the behavior in question

### Decision logic
- If behavior is governed by config and project `.codex/config.toml` conflicts with expectation, classify as config precedence mismatch.
- Apply precedence: project config → repo AGENTS → repo docs → global config → memory.

### Most likely causes
1. incorrect project `.codex/config.toml`
2. expectation based on another repo’s settings
3. global config assumed to override project config
4. repo guidance updated without matching config update

### Safest next steps
1. inspect `.codex/config.toml`
2. compare with expected behavior and repo docs
3. change the smallest necessary config value
4. record the old value before change

### Rollback
- restore the previous config value/file
- note which project-specific setting was changed

### What to document if fixed
- exact config key
- intended value
- why project-level config must win here

---

## Pattern: codex_agents_hierarchy_conflict

**Layer:** Codex  
**Classification:** Instruction / configuration interaction issue

### Typical symptoms
- behavior differs by folder within the same repo
- root instructions appear ignored only in one directory
- user expects one behavior but a closer AGENTS file is overriding it

### Required evidence
- global `~/.codex/AGENTS.md` if relevant
- repo root `AGENTS.md`
- subdirectory `AGENTS.md` files
- working directory/task path

### Evidence that strengthens confidence
- closer AGENTS file contains conflicting instructions
- behavior aligns with nearer-folder instructions instead of root
- issue disappears when the correct scope is understood

### Evidence that weakens confidence
- no nested AGENTS files exist
- behavior is controlled by config rather than instruction hierarchy

### Decision logic
- If multiple AGENTS files exist, inspect hierarchy before concluding Codex ignored instructions.
- Closer file wins. Project root overrides global; folder-level overrides project root.

### Most likely causes
1. subdirectory override not noticed
2. root instructions outdated relative to folder-specific workflow
3. user/global instructions conflicting with project assumptions

### Safest next steps
1. identify active working directory
2. locate all relevant AGENTS files
3. resolve contradictions explicitly
4. move stable general rules upward, folder-specific rules downward

### Rollback
- revert the conflicting AGENTS edit
- document which scope owns which rule

### What to document if fixed
- hierarchy map
- owning file for each class of rule
- conflict resolution note

---

## Pattern: codex_config_vs_instruction_conflict

**Layer:** Codex  
**Classification:** Mixed configuration / behavior issue

### Typical symptoms
- written workflow says one thing, actual behavior follows another
- tools, MCP, permissions, or execution settings behave differently than AGENTS guidance implies

### Required evidence
- `.codex/config.toml`
- relevant `AGENTS.md`
- observed behavior in the governed area

### Evidence that strengthens confidence
- AGENTS guidance conflicts directly with config setting
- governed area is execution/tooling, not just reasoning

### Evidence that weakens confidence
- only one source actually addresses the behavior
- issue is from missing docs rather than conflict

### Decision logic
- Use config for execution behavior, tool settings, MCP, and permissions.
- Use AGENTS for workflow rules, reasoning guidance, and coding standards.
- If the conflict is unclear, inspect both explicitly and do not guess.

### Most likely causes
1. AGENTS updated but config not updated
2. config updated without matching documentation
3. mistaken belief that AGENTS controls a config-governed setting

### Safest next steps
1. identify whether the behavior is execution/tooling or workflow/reasoning
2. inspect both config and AGENTS
3. reconcile the smaller source of drift
4. document the boundary explicitly

### Rollback
- restore previous config or AGENTS text as appropriate
- preserve the diff

### What to document if fixed
- which source governs which behavior
- exact conflict and resolution

---

## Pattern: memory_overreliance_or_missing_persistence

**Layer:** Codex  
**Classification:** Behavior / documentation issue

### Typical symptoms
- recurring facts are repeatedly forgotten or restated
- behavior is unstable across sessions because key knowledge lives only in memory
- user keeps having to restate stable environment facts

### Required evidence
- absence of durable repo documentation for the recurring fact
- repeated user restatement or repeated correction pattern
- records/docs missing the rule

### Evidence that strengthens confidence
- knowledge exists only in chat history or memory
- issue disappears after writing it into repo docs/records
- same correction happens across multiple related tasks

### Evidence that weakens confidence
- repo already clearly documents the rule and behavior still diverges for another reason
- issue is actually config/instruction conflict rather than persistence gap

### Decision logic
- If an important stable fact is not written in repo/docs/records, treat memory overreliance as likely.
- Move persistent facts to checked-in docs instead of relying on repeated chat reminders.

### Most likely causes
1. stable facts not written into repo
2. decision history not recorded
3. unclear ownership of environment facts
4. reliance on memory instead of durable records

### Safest next steps
1. identify the fact/rule that keeps drifting
2. place it into `AGENTS.md`, `docs/`, `runbooks/`, or `records/` as appropriate
3. keep memory supplemental, not primary

### Rollback
- typically not needed; this is usually additive
- if wrong info was written, revert the doc change and correct it

### What to document if fixed
- exact fact/rule
- best storage location
- why it must persist

---

## Pattern: mcp_or_tool_access_failure

**Layer:** Codex  
**Classification:** Tooling / integration issue

### Typical symptoms
- expected tools are unavailable
- MCP-connected capabilities fail or do not appear
- behavior degrades because context/tooling cannot be reached
- repo instructions mention tools that are not operational

### Required evidence
- relevant tool/MCP config
- error output or missing-tool behavior
- repo docs/runbooks for the intended tool path

### Evidence that strengthens confidence
- tool config invalid or missing
- errors directly identify tool/MCP connection failure
- repo expects tools that are not actually configured

### Evidence that weakens confidence
- tool is present and healthy, but behavior issue comes from instructions instead
- failure is due to external network path rather than tool config

### Decision logic
- If the tool path is expected but config or connection is missing/broken, classify as tool/MCP failure.
- Do not rewrite instructions first if the real issue is missing tool access.

### Most likely causes
1. invalid MCP/tool config
2. repo assumptions ahead of actual environment setup
3. permissions/trust/access settings preventing tool use
4. external connection issue affecting tool path

### Safest next steps
1. inspect the relevant MCP/tool config
2. compare expected vs actual available tools
3. fix the smallest configuration problem first
4. preserve prior config before editing

### Rollback
- restore prior MCP/tool config
- document exact path changed

### What to document if fixed
- exact tool config
- trust/permission assumptions
- repo instructions that depend on the tool

---

## Pattern: repo_ambiguity_or_missing_operating_docs

**Layer:** Codex  
**Classification:** Instruction / documentation issue

### Typical symptoms
- assistant makes broad assumptions
- project workflow is unclear
- repeated need to explain architecture, topology, or handoff rules
- multiple plausible workflows exist with no clear canonical one

### Required evidence
- repo docs/runbooks/records inventory
- absence of clear environment topology or operating rules
- repeated ambiguity in prior tasks

### Evidence that strengthens confidence
- no environment-topology doc
- no handoff doc
- no failure-pattern usage guidance
- no durable record of prior fixes

### Evidence that weakens confidence
- repo docs are explicit and current
- issue is actually config-controlled

### Decision logic
- If the repo lacks the docs needed to choose correctly between multiple plausible actions, treat repo ambiguity as a strong candidate.
- Fix the ambiguity in docs rather than relying on repeated verbal clarification.

### Most likely causes
1. missing topology doc
2. missing handoff runbook
3. missing durable records
4. unstable instructions spread across chat instead of repo

### Safest next steps
1. identify the missing decision-critical doc
2. create/update the smallest canonical doc
3. reference it from `AGENTS.md` or index docs

### Rollback
- revert doc addition if inaccurate
- replace with corrected version

### What to document if fixed
- exact new canonical doc
- why it was necessary
- where future related knowledge belongs

---

# CROSS-LAYER / INTEGRATION

## Pattern: codex_openclaw_handoff_ambiguity

**Layer:** Cross-layer integration  
**Classification:** Integration / handoff issue

### Typical symptoms
- Codex reasoning appears sound but OpenClaw execution path is wrong or incomplete
- OpenClaw issue is diagnosed as Codex issue, or vice versa
- responsibilities between “brain” and “tooling” are unclear
- repeated confusion about where a fix should live: repo docs, Codex config, or OpenClaw config

### Required evidence
- handoff runbook if present
- comparison of Codex guidance vs OpenClaw execution/log behavior
- current environment architecture docs

### Evidence that strengthens confidence
- no clear handoff/runbook exists
- repeated misclassification between layers
- fix attempts oscillate between Codex and OpenClaw without decisive evidence

### Evidence that weakens confidence
- failure is clearly isolated to a single layer with strong evidence
- handoff is explicit and being followed correctly

### Decision logic
- If the issue is fundamentally about unclear boundary or responsibility between reasoning and execution, classify as handoff ambiguity.
- Do not keep changing both layers at once.

### Most likely causes
1. no clear handoff runbook
2. unclear ownership of behavior/config
3. diagnosis jumping layers too early
4. missing topology or operating docs

### Safest next steps
1. classify primary failing layer first
2. inspect handoff documentation
3. fix the owning layer before changing the adjacent layer
4. document the boundary if it was unclear

### Rollback
- revert any documentation/config changes that blurred the boundary further
- restore previous known-good handoff guidance if available

### What to document if fixed
- exact boundary between Codex and OpenClaw
- examples of where fixes belong
- escalation path between layers

---

## Pattern: multi_factor_failure_with_false_primary_signal

**Layer:** Cross-layer integration  
**Classification:** Mixed / ambiguous issue

### Typical symptoms
- one symptom dominates attention, but deeper evidence points elsewhere
- a visible network issue hides a config issue, or vice versa
- partial fixes improve one symptom but do not restore stability

### Required evidence
- at least two evidence sources from different layers
- clear chronology of what changed first
- logs/status/config or repo docs showing more than one plausible cause

### Evidence that strengthens confidence
- multiple patterns partially match
- no single change restores health
- one layer recovers but overall behavior remains broken

### Evidence that weakens confidence
- a single pattern clearly explains all symptoms
- a simple rollback fully restores health

### Decision logic
- If more than one strong candidate exists, rank them and do not collapse them into one cause prematurely.
- Resolve the primary layer first, then re-evaluate the secondary signal.

### Most likely causes
1. simultaneous config and network issue
2. pre-existing instability exposed by a new change
3. false primary symptom distracting from the actual root cause

### Safest next steps
1. preserve evidence from all implicated layers
2. order causes by strongest evidence and chronology
3. apply one controlled reversible change at a time
4. re-check after each change without loops

### Rollback
- revert each change independently
- keep a timeline of what was altered and when

### What to document if fixed
- primary vs secondary cause
- why the initial signal was misleading
- improved diagnostic ordering

---

# QUICK PATTERN INDEX

Use this index for rapid first-pass routing.

### OpenClaw
- `node_token_mismatch`
- `gateway_crash_loop`
- `resource_pressure_disk_or_memory`
- `verbose_tool_spam`
- `provider_auth_misread`

### Network
- `tls_or_port_443_failure`
- `support_pack_api_unreachable`

### Codex
- `instruction_drift_or_incomplete_guidance`
- `codex_config_precedence_mismatch`
- `codex_agents_hierarchy_conflict`
- `codex_config_vs_instruction_conflict`
- `memory_overreliance_or_missing_persistence`
- `mcp_or_tool_access_failure`
- `repo_ambiguity_or_missing_operating_docs`

### Cross-layer
- `codex_openclaw_handoff_ambiguity`
- `multi_factor_failure_with_false_primary_signal`

---

# Extension rules for future patterns

When adding a new pattern, include all of the following:

- **Layer**
- **Classification**
- **Typical symptoms**
- **Required evidence**
- **Evidence that strengthens confidence**
- **Evidence that weakens confidence**
- **Decision logic**
- **Most likely causes**
- **Safest next steps**
- **Rollback**
- **What to document if fixed**

Do not add shallow patterns that jump directly from symptom to fix without evidence gating.

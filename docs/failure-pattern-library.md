# Failure Pattern Library

## OpenClaw

### token_mismatch
Symptoms:
- node auth errors
Fix:
- re-pair node

### crash_loop
Symptoms:
- repeated restarts
Fix:
- inspect logs
- config diff

### TLS_failure
Symptoms:
- connection issues
Fix:
- check firewall / ISP

---

## Codex

### instruction_drift
Symptoms:
- inconsistent behavior
Fix:
- update AGENTS.md

### config_mismatch
Symptoms:
- unexpected behavior
Fix:
- inspect .codex/config.toml

### memory_failure
Symptoms:
- forgetting context
Fix:
- move to repo docs

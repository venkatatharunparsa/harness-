# Cursor Enforcement & Hook Capabilities Research

**Date:** 2026-08-20  
**Status:** Evidence-based findings from Research Sprint 1  
**Method:** Deep research with primary sources, community reports, and controlled experiment recommendations.

## 1. Executive Summary

Cursor provides several hooks and settings that can **deterministically block** some agent actions, but they are **not a complete enforcement boundary** because:

- `.cursor/rules/*.mdc` are **guidance only**, not enforcement. The agent can ignore them.
- `beforeShellExecution` can block shell commands by inspecting the full command string and returning `permission: "deny"`.
- `preToolUse` can block direct file edits (Write, Edit, MultiEdit, Delete) by checking `tool_input.file_path`.
- `beforeSubmitPrompt` can block prompt submission but **cannot pause for human approval**.
- `External File Protection` blocks file tools outside workspace but **not shell commands**.
- Background file watchers can detect changes but cannot block them.
- MCP elicitation has a hardcoded ~60-second timeout, making it unsuitable as the only human approval mechanism.

Therefore, our enforcement must use **layered defense**:

1. `preToolUse` hook to block file edits to protected paths.
2. `beforeShellExecution` hook to block shell commands targeting protected paths.
3. `.cursorignore` to hide protected files from agent file tools and indexing.
4. State files stored **outside the workspace root**.
5. A background watcher for detection and alerting.
6. Human approval implemented as a **state machine with Cursor commands**, not a blocking MCP tool.

## 2. Research Questions & Answers

### Q1. What exactly can `beforeShellExecution` block?

**Answer:** It can inspect the full shell command string and working directory (`cwd`), and can block commands by returning `permission: "deny"` or exit code 2. It cannot inspect environment variables of the gated command, and it cannot directly inspect repo state unless the hook runs its own commands.

**Confidence:** VERIFIED for command blocking; INFERRED for repo-state inspection.

**Key implications:**

- We can block shell writes to `.team/state.json`, `.system-gate/`, `.sentinel/` using regex matcher like `\.team/state\.json|\.system-gate/|\.sentinel/`.
- Must set `failClosed: true` to prevent bypass on hook failure.
- Use one consolidated hook script due to reported multiple-hooks array bug.

### Q2. Can Cursor block direct file editing tools?

**Answer:** Yes. `preToolUse` fires before all tool types, including Write/Edit/MultiEdit/Delete. It can block via `permission: "deny"` or exit code 2, and can access `tool_input.file_path` to check protected paths.

**Confidence:** VERIFIED for `preToolUse` blocking; `tool_input.file_path` structure VERIFIED for Claude Code and INFERRED for Cursor (must validate in our environment).

**Key implications:**

- This is our primary prevention for direct file edits.
- Use matcher `Write|Edit|MultiEdit|Delete`.
- Use one hook script that checks path against protected patterns.

### Q3. How does `beforeSubmitPrompt` work?

**Answer:** It can block prompt submission by returning `{ "continue": false, "user_message": "..." }` or exit code 2. It is synchronous and **cannot pause or wait for human approval**.

**Confidence:** VERIFIED.

**Key implications:**

- Do not use `beforeSubmitPrompt` as the human approval mechanism.
- We can use it to block user prompts when the project is in `pending_approval` or `blocked` state, with a clear message.

### Q4. How reliably do `.cursor/rules/*.mdc` force tool calls?

**Answer:** Rules are **guidance, not enforcement**. Compliance is probabilistic, not deterministic. No published compliance rates exist for rules like "query code graph before edit". Best practices improve adherence but do not guarantee it.

**Confidence:** VERIFIED.

**Key implications:**

- Rules are only layer 1; never rely on them for security or workflow enforcement.
- Use strong language (`MUST`, `NEVER`), `alwaysApply: true`, keep rules short, and pair with hooks.
- We must measure our own compliance rates.

### Q5. Is a background filesystem watcher practical?

**Answer:** Yes. Python `watchdog` or Rust `notify` are cross-platform and production-proven for detection. But user-mode watchers **cannot block modifications**; blocking requires kernel drivers, which are impractical. Watchers can send alerts and can expose MCP tools for Cursor to query.

**Confidence:** VERIFIED.

**Key implications:**

- Use a lightweight watcher for detection, alerting, and integrity checks.
- It is not a prevention boundary; combine with hooks.
- Watcher can expose MCP tools like `check_file_integrity` and `get_recent_modifications`.

### Q6. Where should `.team/state.json` live so the agent cannot edit it?

**Answer:** Store it **outside the workspace root** (e.g., `~/.cth/`), add it to `.cursorignore`, set read-only permissions for the Cursor user, and allow writes only through `team-workflow` MCP.

**Confidence:** VERIFIED for file tools and shell bypass; INFERRED for database being stronger.

**Key implications:**

- `.cursorignore` blocks agent file tools and indexing from reading/writing the state path.
- `preToolUse` and `beforeShellExecution` provide enforcement against file tool and shell attempts.
- `team-workflow` MCP is the only writer.
- Background watcher detects direct OS-level tampering.

### Q7. What is the best human approval mechanism inside Cursor?

**Answer:** MCP elicitation is the most native and lowest-friction, but it has a 60-second timeout and uncertain Cursor support. The practical choice for our harness is **Cursor commands (`/team:approve`, `/team:reject`) combined with a state machine**. This avoids timeout risk and gives real human control.

**Confidence:** VERIFIED.

**Key implications:**

- `team_request_approval` sets state to `pending_approval` and returns immediately.
- Agent stops and waits; human runs `/team:approve` or `/team:reject`.
- `team_advance` checks state before allowing transition.
- We may add MCP elicitation later if Cursor support stabilizes.

### Q8. Does Cursor support long-running MCP calls or role-based shell allowlists?

**Answer:** No. MCP elicitation hardcoded ~60s timeout; general MCP tools ~30s–2min, not configurable. Command allowlists/denylists exist but are not role-based. `beforeShellExecution` remains the most reliable deterministic shell blocker.

**Confidence:** VERIFIED.

**Key implications:**

- Do not design approval as a blocking MCP tool.
- Use `beforeShellExecution` for shell command blocking.
- Use `preToolUse` for file tool blocking.
- Role-based shell restrictions are not available; we enforce via our team-workflow MCP and hooks.

## 3. Consolidated Evidence Table

| # | Claim | Confidence | Source Type |
|---|---|---|---|
| 1 | `beforeShellExecution` can inspect command and cwd | VERIFIED | Official Cursor docs |
| 2 | `beforeShellExecution` can block via `permission: deny` | VERIFIED | Official Cursor docs |
| 3 | `beforeShellExecution` cannot inspect env vars | VERIFIED | Official docs + community |
| 4 | `preToolUse` fires before all tool types | VERIFIED | Official Cursor docs |
| 5 | `preToolUse` can block via deny or exit 2 | VERIFIED | Official Cursor docs |
| 6 | `tool_input.file_path` available for Write/Edit | VERIFIED (Claude) / INFERRED (Cursor) | Claude hooks docs + community |
| 7 | `beforeSubmitPrompt` can block but cannot wait | VERIFIED | Official Cursor docs |
| 8 | Cursor rules are guidance, not enforcement | VERIFIED | Empirical study, security analyses |
| 9 | Background watcher can detect but not block | VERIFIED | OS docs + library docs |
| 10 | State file outside workspace + .cursorignore + hooks is strong protection | VERIFIED (components) | Official docs + community |
| 11 | MCP elicitation timeout ~60s, hardcoded | VERIFIED | MCP SDK issue + community |
| 12 | General MCP timeout ~30s–2min, not configurable | VERIFIED | Community reports |
| 13 | Command allowlist/denylist not role-based | VERIFIED | Official Cursor docs |
| 14 | `beforeShellExecution` more reliable than allowlists | VERIFIED | Community security guides |
| 15 | External File Protection blocks file tools but not shell | VERIFIED | Official + community |

## 4. Final Enforcement Architecture

### Prevention
- `preToolUse` hook: blocks direct file edits to protected paths.
- `beforeShellExecution` hook: blocks shell commands targeting protected paths.
- `.cursorignore`: prevents agent file tools and indexing from seeing protected files.
- State files outside workspace, read-only where possible.

### Detection
- Background watcher (`watchdog`/`notify`) monitors state/gate files.
- Watcher exposes MCP tools for integrity queries.
- All suspicious changes logged to audit log and optionally alert human.

### Approval
- State machine in `team-workflow` MCP.
- Agent requests approval; state becomes `pending_approval`.
- Human approves/rejects via Cursor commands `/team:approve`, `/team:reject`.
- `team_advance` checks state before allowing transition.

### Rules
- `.cursor/rules/*.mdc` guide agent behavior.
- Use `alwaysApply: true`, strong language, and explicit tool names.
- Never rely on rules for enforcement.

## 5. Open Questions & Risks

- `tool_input.file_path` must be validated in our exact Cursor/Windows setup.
- Windows hook behavior may differ; test project-level hooks and PowerShell/Bash interactions.
- MCP elicitation support in Cursor is uncertain; we may adopt later if timeout becomes configurable.
- Multiple hooks in array may only run first; use one consolidated hook script.
- Background watcher detection but not blocking; rely on hooks for prevention.
- The agent can still use shell to `chmod` read-only files if same user; mitigation: store state outside workspace and monitor.

## 6. Decisions Influenced

- Enforcement layer: hooks + external state + watcher, not rules.
- Approval UX: state machine + commands, not blocking MCP.
- State storage: outside workspace, `.cursorignore`, MCP-only writes.

## 7. Appendix: Primary Sources

- Cursor hooks docs: https://cursor.com/docs/agent/hooks
- Cursor security docs: https://cursor.com/docs/agent/security
- Cursor MCP docs: https://cursor.com/docs/mcp
- MCP spec elicitation: https://modelcontextprotocol.io/specification/2026-07-28/client/elicitation
- "Beyond the Prompt" study: https://arxiv.org/pdf/2512.18925.pdf
- Knostic Cursor security analysis: https://www.knostic.ai/blog/cursor-does-not-follow-rules
- Python watchdog: https://pypi.org/project/watchdog/
- Rust notify: https://github.com/notify-rs/notify
- Official Cursor Enterprise RBAC: https://cursor.com/docs/enterprise/identity-and-access-management

Full detailed findings and community source links are preserved in the research session history.

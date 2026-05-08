# Session Logger Help

Use Session Logger by explicitly invoking it, for example:

```text
$session-logger help
```

`help all` prints this same overview.

## Project Setup

```text
$session-logger index projects
$session-logger list projects
$session-logger init project my-project
```

- `index projects` creates or updates the local `.agents/projects-index.json` registry from immediate `projects/` folders and scaffold/config existence only.
- `list projects` shows short IDs, full IDs, names, paths, scaffold status, access mode, write policy, and last activation dates.
- `init project my-project` normalizes the name, creates the workflow scaffold, registers the project, and activates it as primary.
- Projects marked `external_read_only` in the local registry cannot be initialized or written by Session Logger.

## Activation

```text
$session-logger activate my-project
$session-logger activate 12345678
$session-logger activate app + docs-site, api-service
$session-logger activate + docs-site
$session-logger deactivate app
$session-logger deactivate all
```

- The primary project is read/write for Session Logger workflow memory.
- Projects after `+` are allowed read-only projects.
- `activate + docs-site` adds a read-only project without changing the current primary.
- Project refs may be names, aliases, short IDs, or full UUIDs.
- External read-only projects can be added only with `activate + <project-ref>` after a managed primary is active; they can never become primary.
- `deactivate` is canonical. `disactivate` is tolerated as an alias.

## Session Flow

```text
$session-logger start
$session-logger mid
$session-logger end
```

- `start` recovers from hot memory after activation, then inspects `docs/workflow/auto_recovery.md` only for unresolved automatic safety entries, unmatched BEFORE entries, `open` / `unclosed` statuses, or non-template entries that affect restart accuracy.
- This `auto_recovery.md` inspection is hot-adjacent recovery, not warm/cold/freezing access, and unresolved auto-recovery state is surfaced before stale-hot warm recovery is requested.
- If no project is active, `start` can suggest recent projects before reading project memory.
- `mid` manually blends current context and relevant automatic recovery entries into the primary project's hot summary.
- `end` closes the session, writes logs, and may attempt scoped git reconciliation.

## Automatic Safety Capture

When a primary project is active, agents must append automatic safety entries before and after every accepted implementation plan execution. The user does not need to spell `$session-logger mid` for this narrow safety capture.

- Automatic entries append to `docs/workflow/auto_recovery.md`; they do not overwrite `last_session_summary.md`.
- Before execution, the entry captures timestamp, trigger, plan ID, prior state, intended plan, decisions/assumptions, expected scope, and risks.
- After execution and verification, the entry uses the same plan ID and captures actual changes, decisions made during execution, verification, remaining work, and the BEFORE -> NOW delta.
- Automatic non-plan safety entries are allowed for context pressure, before `/compact`, before `/fork`, or a major decision/milestone.
- This does not apply to read-only exploration, planning-only turns, or non-mutating checks.
- If hot recovery has not happened in the current thread, the agent reads only `project_identity.md` and `last_session_summary.md` before appending the entry.
- If no primary project is active, required files are missing, or append fails, execution is blocked and the failure is reported.

Manual `mid` and session `end` merge useful `auto_recovery.md` entries into normal session memory and remove duplicates. `end` clears `auto_recovery.md` only after the merge succeeds.

## Audit

```text
$session-logger audit framework
```

- Runs the manual framework audit for the primary active project only.
- Proposed upgrade actions must be specific and approval-gated.

## Safety Notes

- `help` prints this file only; it does not read project memory or the project index.
- `index projects` never reads project code or workflow memory contents.
- The live `.agents/projects-index.json` registry is local ignored state. The tracked schema example is `.agents/projects-index.example.json`.
- Warm, cold, and freezing memory still require explicit permission.
- Allowed read-only projects must not receive checkpoints, session logs, framework upgrades, commits, or memory writes.
- External read-only projects have `write_policy: forbidden`; Session Logger must block init, primary activation, checkpoints, session end, automatic safety entries, scaffold writes, framework upgrades, commits, and memory writes for them.
- Automatic safety entries write only `auto_recovery.md` in the active primary project and do not grant deeper memory access.
- If session end finds only Session Logger memory/log changes, it asks before committing those logger-only changes.

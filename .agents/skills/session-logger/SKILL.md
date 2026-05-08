---
name: session-logger
description: Manage project-scoped session memory for this workspace when explicitly evoked as Session Logger, $session-logger, or by direct link to this skill, plus forced automatic append-only safety entries when a primary project is active. Supports help, project indexing, init project, primary/read-only activation, start, manual mid, end, audit framework, hot/warm recovery, automatic auto_recovery capture, checkpoint blending, session close, scoped one-time git commit attempts, and post-commit log reconciliation. Never run from plain session words or memory phrases unless Session Logger was explicitly evoked, except for automatic safety entries. Requires explicit project activation before reading or writing live project memory.
---

# Session Logger Skill

Manage session memory inside one primary explicitly activated project. Optional additional activated projects are read-only and never receive Session Logger writes.

Use `docs/frameworks/session-logger/` only as shared reference/scaffolding. Do not recover from it as live state.

Use the local `.agents/projects-index.json` as the repo-level project registry. It is local ignored shared infrastructure, not project memory. The public tracked schema example is `.agents/projects-index.example.json`.

Use `.agents/skills/session-logger/HELP.md` as the canonical printed help text.

## Invocation gate

Do not perform any Session Logger action unless the user explicitly evokes this skill in the same request with `$session-logger`, `Session Logger`, or a direct skill link.

Exception: automatic safety entries may run without the user spelling `$session-logger mid` when all of these are true:

- a primary Session Logger project is already active
- the project is initialized and current-thread hot recovery is complete, or hot recovery can be completed by reading only `project_identity.md` and `last_session_summary.md`
- the user accepted a project-scoped implementation plan that is about to mutate project or session-scoped state or has just completed execution/verification, or a narrow non-plan safety event is occurring
- the entry appends only to the primary project's `docs/workflow/auto_recovery.md`

Root shared-infrastructure work may proceed without a primary project. Do not create root-level shared memory for root-only work, and do not write automatic safety entries unless a primary project is active and the work is being captured for that project.

If no primary project is active for project-scoped work that requires automatic safety capture, block execution and ask the user to activate or start a primary project. If hot recovery cannot be completed, `auto_recovery.md` is unavailable, or the append fails, execution or final delivery must stop and report the failure.

After explicit evocation, interpret only these short commands:

- `help` = print `.agents/skills/session-logger/HELP.md` verbatim without reading project state
- `help all` = alias for `help`
- `init <project-name>` = legacy alias for `init project <project-name>` when unambiguous
- `init project <project-name>` = normalize a new project name, create/register the project, and activate it as primary
- `index projects` = create or update the local `.agents/projects-index.json` from immediate project directory names plus scaffold/config existence only
- `list projects` = list indexed projects without reading project memory
- `activate <project-ref>` = activate the named project as primary
- `activate <primary-ref> + <allowed-ref>, <allowed-ref>` = activate a primary read/write project plus allowed read-only projects
- `activate + <allowed-ref>, <allowed-ref>` = add allowed read-only projects without changing the current primary
- `start` = recover from hot memory
- `mid` = manually checkpoint the session by curating hot summary memory
- `end` = close the session
- `deactivate <project-ref>` = remove a project from the active set
- `deactivate all` = clear all active project permissions for the conversation
- `disactivate ...` = tolerated alias for `deactivate ...`
- `audit framework` = run a manual framework audit for the primary active project

Do not treat plain `help`, `help all`, `init`, `init project`, `index projects`, `list projects`, `start`, `mid`, `end`, `activate`, `deactivate`, `disactivate`, `audit framework`, `Hot only`, `Check warm`, `Go cold`, `Deep recovery`, `Full recovery`, `Checkpoint`, or `Do not burn tokens` as permission to use this skill unless Session Logger was explicitly evoked, except for automatic safety entries.

For `help` and `help all`:

1. Read only `.agents/skills/session-logger/HELP.md`.
2. Print its contents verbatim.
3. Do not read the local `.agents/projects-index.json`.
4. Do not read project workflow files, project code, framework docs, or live memory.
5. Do not summarize or reconstruct the help text from memory.

## Help Maintenance

`HELP.md` may be read only when:

- the user explicitly invokes `help` or `help all`
- the user explicitly asks about Session Logger help
- the work changes Session Logger commands, command syntax, aliases, or command safety rules

Any update to the command list, command syntax, command aliases, or command safety behavior MUST review and update `.agents/skills/session-logger/HELP.md` in the same change.

Normal Session Logger commands such as `activate`, `start`, `mid`, `end`, `audit framework`, `index projects`, `list projects`, and `deactivate` must not read `HELP.md`.

## Command lifecycle

Use this lifecycle:

```text
help|index projects -> init project|activate -> audit framework -> start -> mid/end
```

- `help` prints the maintained help file and does not touch project state.
- `index projects` refreshes the repo-level registry without reading project code or memory.
- `init project` prepares an uninitialized project for Session Logger use, registers it, and activates it as primary.
- `activate` chooses the primary read/write project and optional allowed read-only projects.
- `audit framework` manually checks the primary active project against the current shared framework methodology.
- `start` recovers from hot memory plus bounded hot-adjacent `auto_recovery.md` restart markers after init exists.
- `mid` manually blends current context and relevant automatic entries into a curated hot-summary checkpoint.
- automatic safety entries append to `auto_recovery.md` before/after accepted project-scoped implementation plan execution and for narrow context-pressure, `/compact`, `/fork`, or major-milestone events.
- `end` writes closeout logs, merges and clears `auto_recovery.md` after successful incorporation, and may attempt scoped commit reconciliation.

If `activate` or `start` selects a project that has no workflow scaffold yet, instruct the user to run `init project <project-name>` instead of acting as if recovery can proceed.

## Project scope and initialization

`init project <project-name>` is the only Session Logger command meant for an uninitialized project.

For `init project`:

1. Confirm the user explicitly named the project.
2. Normalize the provided name: lowercase, trim, convert spaces/underscores to hyphens, remove unsupported characters, collapse repeated hyphens, and reject empty/colliding names with a concrete suggested alternative.
3. Create `projects/<normalized-name>/docs/workflow/` from the shared scaffold.
4. Register the project in the local `.agents/projects-index.json` with an immutable UUID v4 `id`, a `short_id` derived from the first UUID segment, `access_mode: "managed"`, and `write_policy: "allowed"`.
5. Create `projects/<normalized-name>/.codex/session-logger.toml` if the project does not already have framework audit state, and store the project ID when practical.
6. Set the adopted framework date to the current shared framework update date during explicit framework init.
7. Do not write real session memory during init.
8. Immediately activate the new project as primary so the user can start work.

For legacy `init <project-name>`:

- Treat it as `init project <project-name>` only when the intent is unambiguous.
- Prefer documenting and using `init project <project-name>` going forward.

For `index projects`:

1. Read and update only the local `.agents/projects-index.json`, creating it with the tracked `.agents/projects-index.example.json` shape when missing.
2. Inspect only immediate child directory names under `projects/`.
3. Check only whether Session Logger scaffold/config paths exist.
4. Never read project code, project memory file contents, archive contents, or recurse broadly through project trees.
5. Preserve existing project IDs and short IDs.
6. Delete registry entries whose project directory no longer exists.
7. Preserve existing access metadata when a project remains present.
8. Assign a new UUID only when registering a newly discovered project, derive `short_id` from the first UUID segment, set `access_mode: "managed"` and `write_policy: "allowed"` by default, and regenerate if either the full ID or short ID collides.

For `list projects`:

- Read only the local `.agents/projects-index.json`.
- Show short ID first, plus full ID, canonical name, path, initialized/scaffold status, access mode, write policy, aliases, and last activation date.

Registry access metadata:

- `access_mode: "managed"` means the project can become primary when explicitly activated and all normal Session Logger write rules are satisfied.
- `access_mode: "external_read_only"` means the project is an external reference repository. It can be listed and resolved, but it can only be added as an allowed read-only project.
- `write_policy: "forbidden"` means Session Logger must not run `init project`, `mid`, `end`, automatic safety entries, framework upgrades, scaffold writes, memory writes, session-end logs, commits, or any other write action for that project.
- If a user tries to activate an external read-only project as primary, stop and explain that it can only be added with `$session-logger activate + <project-ref>` after a managed primary is active.

For project references:

- Accept canonical name, alias, `short_id`, or full UUID `id`.
- Treat the full UUID `id` as the durable registry identity.
- Treat `short_id` as a convenience reference for user commands and lists.
- Do not silently choose a project if a reference is ambiguous; show the matching candidates and ask for a clearer project ref.

## Required project activation

Before live memory work:

1. Confirm the user explicitly activated a primary project by project ref, or selected one through `start` project selection.
2. Confirm the primary project's registry record has `access_mode: "managed"` and does not have `write_policy: "forbidden"`.
3. Resolve writable Session Logger memory only under the primary project's `docs/workflow/`.
4. Treat projects after `+` in `activate <primary-ref> + <allowed-ref>, <allowed-ref>` as read-only.
5. Do not inspect any allowed read-only project unless the current user request explicitly needs it.
6. Never write Session Logger memory, checkpoints, framework upgrades, commits, or session-end logs to allowed read-only projects.

For `activate + <allowed-ref>, <allowed-ref>`:

1. Require an existing primary active project.
2. Keep the current primary unchanged.
3. Add the named projects as allowed read-only projects.
4. If no primary project is active, ask the user to activate a primary project first.

External read-only projects:

1. May be listed and resolved by canonical name, alias, `short_id`, or full UUID.
2. May be added only as allowed read-only references with `activate + <project-ref>`.
3. Must never become primary.
4. Must never receive Session Logger writes, scaffold changes, framework upgrades, automatic safety entries, session-end logs, commits, or init actions.

For `activate <project-ref>` when a different primary project is already active:

1. Pause before replacing the primary.
2. Ask whether the user intends to switch the primary project.
3. If the user confirms switching primary, activate the requested project as primary.
4. If the user says the intent was to add an allowed read-only project instead, add it as read-only and remind them that the explicit format is `$session-logger activate + <project-ref>`.

If no project is active and the command is `start`, read only the local `.agents/projects-index.json`, suggest the last three activated projects as `1`, `2`, `3`, or `Other`, and activate the selected project before recovery. If the user selects `Other`, list indexed projects excluding the recent three and ask for a project ref.

If the selected project has `access_mode: "external_read_only"` or `write_policy: "forbidden"`, do not activate it as primary. Explain that it can only be added as read-only after a managed primary is active.

If no project is active for checkpoint, session-end, or audit work, ask the user to activate a primary project first.

For project-scoped automatic safety capture, no active primary project is a hard stop: block the plan execution or final delivery until the user activates or starts a primary project. Root-only shared-infrastructure work does not require automatic safety capture and must not create root-level shared memory.

If the active or requested project has `write_policy: "forbidden"`, block any checkpoint, session-end, framework audit upgrade action, automatic safety capture, init action, scaffold write, memory write, or commit for that project.

Reusable context inventory and activation-audit references live under shared framework references:

- `docs/frameworks/session-logger/references/context-inventory-v1.md`
- `docs/frameworks/session-logger/references/context-inventory-examples.md`
- `docs/frameworks/session-logger/references/effective-context-checklist.md`
- `docs/frameworks/session-logger/references/activation-audit.md`

Project-local product docs are product history and decision records, not normative shared framework reference.

## Session topic

Logger commands are control text, never semantic topic text. Do not use `$session-logger start`, `Session Logger start`, or `Start session logger` as a chat/session/log/commit topic.

On activation/start:

1. If no primary project is active, read only the local `.agents/projects-index.json` and guide project selection before memory recovery.
2. Read hot memory from the primary project: `project_identity.md` and `last_session_summary.md`.
3. If `auto_recovery.md` exists, inspect it only for unresolved automatic safety entries, unmatched BEFORE entries, `open` / `unclosed` statuses, or non-template entries that affect restart accuracy.
4. Treat this `auto_recovery.md` inspection as hot-adjacent recovery, not warm/cold/freezing access, and surface unresolved auto-recovery state before asking for warm recovery on stale hot summaries.
5. Parse the date from the hot summary title when possible. Prefer `dd-mm-yyyy` from titles like `Mon, 14-04-2026 | ...`; also accept ISO `yyyy-mm-dd`.
6. If the hot summary is older than 72 hours, ask explicit permission to read `last_session_detailed.md`.
7. If warm access is granted, brief: where we are, what we were doing, what was done, and what is next. Then ask the user to choose the session topic.
8. If hot memory is fresh, warm access is denied, or the date is missing/unparseable, leave the topic pending and derive it from the first non-logger work request.

If a native runtime command exists to rename the chat/thread, use it only after a real topic is known. Otherwise use the derived topic in session logs, archive slugs, and commit messages.

## Startup

Within the primary active project only:

0. If no primary project is active, use the local `.agents/projects-index.json` to suggest recent projects with `1`, `2`, `3`, or `Other`; do not read project memory until a primary project is selected.
0. If the primary project's workflow scaffold does not exist yet, instruct the user to run `init project <project-name>`.
1. Read `project_identity.md`.
2. Read `last_session_summary.md`.
3. Inspect `auto_recovery.md` if present, limited to unresolved automatic safety entries, unmatched BEFORE entries, `open` / `unclosed` statuses, or non-template entries that affect restart accuracy.
4. Apply the session-topic rules above.
5. Brief the current state from hot memory and hot-adjacent restart-relevance markers.
6. Do not escalate below hot or bounded hot-adjacent recovery without explicit permission.

## Permission model

These permissions apply only within the currently primary active project unless the user explicitly names an allowed read-only project and grants read depth for that project:

- `Hot only` = `project_identity.md`, `last_session_summary.md`, and bounded startup inspection of `auto_recovery.md` for unresolved or restart-relevant automatic entries; this is hot-adjacent recovery, not warm/cold/freezing access.
- `Check warm` = allow `last_session_detailed.md`
- `Go cold` = allow `sessions_history/`
- `Deep recovery` = allow warm + cold
- `Full recovery` = allow warm + cold + freezing
- `mid` = manually overwrite summary only after blending current context with relevant automatic entries
- automatic safety entry = forced append to `auto_recovery.md` around accepted project-scoped implementation plan execution and narrow non-plan safety events
- `Do not burn tokens` = keep recovery hot-only and concise

None of these phrases allows access to another project by itself. Allowed projects remain read-only even when read depth is granted.

Framework docs may be used as shared reference material only. They are never live project memory.

## Release C Visibility Surfaces

Session Logger should be able to explain, in the moment:

- why it activated
- what command it interpreted
- what rules constrained the run
- what project is primary
- which projects, if any, are allowed read-only

Use the shared Release C reference docs as the normative visibility surfaces:

- `docs/frameworks/session-logger/references/effective-context-checklist.md` = short manual preflight review
- `docs/frameworks/session-logger/references/activation-audit.md` = compact live explanation block
- `docs/frameworks/session-logger/references/context-inventory-v1.md` = deeper audit surface for included sources

The activation audit explains the current run. The context inventory remains the deeper explanation of included sources, permissions, freshness, and exclusions.

Do not treat the activation audit as permission to widen scope, bypass explicit invocation, or read warm/cold memory without approval.

## Manual framework audit

`audit framework` is manual-only for now. Do not add or imply automatic preflight checks.

Automatic safety capture is not a framework audit, preflight self-check, upgrade check, or permission expansion. It is only forced append-only active-session recovery capture.

The audit must:

1. Require explicit Session Logger invocation and one primary active project.
2. Read only the primary active project's `projects/<project-name>/.codex/session-logger.toml`.
3. Read shared framework update manifests from `docs/frameworks/session-logger/updates/`.
4. Compare the project's adopted framework date against the latest framework update date.
5. Read only newer dated update manifests after the project's adopted date.
6. Evaluate only Session Logger-governed project surfaces in the primary project:
   - `.codex/session-logger.toml`
   - `docs/workflow/`
   - Session Logger procedure and scaffold files under the primary active project
6a. When a newer-than-adopted framework manifest under `docs/frameworks/session-logger/updates/` requires exact scaffold wording validation, the audit may also read the matching shared workflow templates under `docs/frameworks/session-logger/templates/workflow/` strictly to compare against the primary project's governed `docs/workflow/` files. This permission does not extend to deeper shared reference packs and does not authorize edits to shared templates.
7. Produce a framework audit report that states:
   - what changed since adoption
   - how those changes affect project structure, governed files, and governed data
   - what is already compliant
   - what is missing or outdated
   - what is unknown and needs clarification
   - which specific upgrade actions are proposed
8. Ask permission for specific explained actions before applying any change.

The manual framework audit remains manifest-driven. Do not read deep shared reference packs such as `references/context-inventory-v1.md` unless the user explicitly asks for deeper reference review.

If the project has never adopted the framework formally, say so and propose initialization actions instead of pretending version comparison is possible.

Use audit result values:

- `up_to_date`
- `review_needed`
- `blocked`

Allowed action phrasing must be specific, such as:

- create `projects/<project-name>/.codex/session-logger.toml` with adopted framework date `YYYY-MM-DD`
- update `docs/workflow/session_start.md` to the current scaffold wording
- add missing governed workflow file `...`, such as `docs/workflow/auto_recovery.md`
- rewrite governed workflow sections to match the current framework procedure

Do not propose vague actions like `upgrade the project`, `sync framework`, or `make it compliant`.

## Native Codex commands

Use native commands as session-control helpers, not as memory sources:

- `/status` = inspect context/worktree/session state before checkpoint, session end, or a long next step.
- `/compact` = after checkpoint or session end when continuing with a smaller context.
- `/fork` = before risky exploration, alternate approaches, or parallel investigation that should not pollute the main session thread.

Before `/compact` or `/fork`, append an automatic safety entry first if useful state has not already been written to the primary active project's memory.

Automatic safety capture must also run before `/compact` or `/fork` when either command would follow an accepted project-scoped implementation plan.

## Checkpoint

Within the primary active project only:

- Require both init and primary activation before `mid`.
- Manual `mid` reads project identity, current hot summary, current user/session context, and relevant entries from `auto_recovery.md`.
- Manual `mid` overwrites `last_session_summary.md` only after blending durable facts; do not blindly append duplicate automatic notes.
- Run automatic safety capture before the first mutating step of every accepted project-scoped implementation plan, and again after execution/verification before the final response, `/compact`, or `/fork`.
- Apply automatic safety capture to each distinct project-scoped execution plan in long or multi-plan work.
- Use a stable plan/checkpoint ID to pair BEFORE and AFTER automatic entries.
- If a BEFORE entry has no matching AFTER entry, surface it as an unclosed plan marker at later `start`, manual `mid`, or `end`.
- Automatic non-plan safety entries are allowed only for context pressure, before `/compact`, before `/fork`, or a major decision/milestone recognized by the agent.
- Do not run automatic safety capture for read-only exploration, planning-only turns, or non-mutating checks.
- Before automatic capture, confirm current-thread hot recovery; if it has not happened, read only `project_identity.md` and `last_session_summary.md` first.
- Use `/status` first when context pressure or worktree scope is unclear.
- Automatic safety entries append to `auto_recovery.md`; they never overwrite `last_session_summary.md`.
- Manual `mid` overwrites `last_session_summary.md`.
- Mark the hot-summary header as a checkpoint for manual `mid`.
- Do not archive.
- Do not write `last_session_detailed.md`.
- If continuing after a large context, suggest `/compact`.

Automatic entry schema:

- timestamp
- source/trigger
- plan/checkpoint ID when applicable
- concise summary
- important decisions
- risks/blockers
- relevant paths/files
- status: `open`, `resolved`, or `unclosed`

Before-plan entries must capture prior state, intended plan, decisions/assumptions, expected files or scope, and risks.

After-plan entries must capture the same plan/checkpoint ID, original intent summary, actual changes, decisions made during execution, verification, remaining work, and the BEFORE -> NOW delta.

Manual hot-summary checkpoints must contain at minimum: title/checkpoint marker, TL;DR, current state, next recommended step, decisions, risks/watchouts, and references plus git/worktree state when relevant.

## Session end

Within the primary active project only:

1. Require both init and primary activation before `end`, and confirm the primary project is `access_mode: "managed"` with writes allowed.
2. Use `/status` first to confirm scope and unfinished work.
3. Archive previous latest-session files.
4. Read `auto_recovery.md` and merge useful automatic entries into the new `last_session_summary.md` and `last_session_detailed.md`, deduping against existing memory.
5. Write new `last_session_summary.md` and `last_session_detailed.md`.
6. Clear `auto_recovery.md` only after confirming its useful contents were incorporated into the latest session memory. If merge or write fails, leave `auto_recovery.md` untouched.
7. Include grouped visible change state in the logs: `Committed`, `Remaining uncommitted`, `Skipped`, and `Failed`. Before the commit attempt, put visible dirty files under `Remaining uncommitted`.
8. After logging, check whether the primary active project or workspace is a git repository.
9. If at least one non-logger scoped change exists, request one-time permission for a commit transaction. Do not request a persistent approval rule.
10. If only Session Logger memory/log files are dirty, explicitly ask whether to commit those logger-only changes.
11. Skip commit entirely only when there are no meaningful file changes, decisions, or progress to preserve.
12. Stage only primary-project changes plus explicitly allowed repo-level files. Never stage another project.
13. Commit with `type(scope): specific outcome`.
14. For logger-only commits approved by the user, use a factual memory-outcome subject such as `docs(session-logger): record session decisions`.
15. Rerun scoped status.
16. Rewrite only latest session logs, not archive copies, so committed files move from `Remaining uncommitted` to `Committed` and only actual leftovers remain dirty.
17. Stage the rewritten latest logs and amend the same commit.
18. If permission is denied/canceled, commit fails, or amend fails, do not claim success. Leave logs unchanged when no commit happened; if amend fails after commit, report the commit and the remaining dirty log rewrite accurately.
19. If continuing in the same thread after session end, suggest `/compact`.

## Commit message rules

Use concise conventional commit subjects:

```text
<type>(<scope>): <specific outcome>
```

- Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `build`, `ci`.
- Scope is the primary active project or subsystem, such as `meta-cli` or `session-logger`.
- Describe the actual work outcome, not the logging procedure.
- Avoid filler: `capture session end`, `update session log`, `misc changes`, `session cleanup`.
- Logger-only commits are allowed only after an explicit user choice and must describe the durable memory outcome, not mechanics.
- For large sessions, keep the subject short and put grouped details in the commit body.

## Do not do this

- Do not treat `activate` or `start` as implicit bootstrap for an uninitialized project.
- Do not write to allowed read-only projects.
- Do not turn manual framework audit into an automatic preflight check.
- Do not treat automatic safety capture as permission to run any other Session Logger command.
- Do not read project memory before explicit project activation.
- Do not read warm/cold/freezing memory without permission.
- Do not read memory from any non-active project.
- Do not recover from framework docs/templates as if they were live state.
- Do not use logger commands as the session topic.
- Do not use `/fork` to bypass project access rules.
- Do not use `/compact` before writing needed checkpoint/session memory.
- Do not clear `auto_recovery.md` before its useful entries are incorporated into latest session memory.
- Do not stage or commit another project without explicit authorization.
- Do not push to remote unless the user explicitly requests it.
- Do not invent missing work.
- Do not append to giant history logs.

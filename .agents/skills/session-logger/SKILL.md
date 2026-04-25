---
name: session-logger
description: Manage project-scoped session memory for this workspace only when explicitly evoked as Session Logger, $session-logger, or by direct link to this skill. Supports help, project indexing, init project, primary/read-only activation, start, mid, end, and audit framework for project bootstrap, activation, hot/warm recovery, checkpointing, session close, manual framework audit, scoped one-time git commit attempts, and post-commit log reconciliation. Never run from plain session words or memory phrases unless Session Logger is explicitly evoked in the same request. Requires explicit project activation before reading or writing live project memory.
---

# Session Logger Skill

Manage session memory inside one primary explicitly activated project. Optional additional activated projects are read-only and never receive Session Logger writes.

Use `docs/frameworks/session-logger/` only as shared reference/scaffolding. Do not recover from it as live state.

Use `.agents/projects-index.json` as the repo-level project registry. It is shared infrastructure, not project memory.

Use `.agents/skills/session-logger/HELP.md` as the canonical printed help text.

## Invocation gate

Do not perform any Session Logger action unless the user explicitly evokes this skill in the same request with `$session-logger`, `Session Logger`, or a direct skill link.

After explicit evocation, interpret only these short commands:

- `help` = print `.agents/skills/session-logger/HELP.md` verbatim without reading project state
- `help all` = alias for `help`
- `init <project-name>` = legacy alias for `init project <project-name>` when unambiguous
- `init project <project-name>` = normalize a new project name, create/register the project, and activate it as primary
- `index projects` = update `.agents/projects-index.json` from immediate project directory names plus scaffold/config existence only
- `list projects` = list indexed projects without reading project memory
- `activate <project-ref>` = activate the named project as primary
- `activate <primary-ref> + <allowed-ref>, <allowed-ref>` = activate a primary read/write project plus allowed read-only projects
- `activate + <allowed-ref>, <allowed-ref>` = add allowed read-only projects without changing the current primary
- `start` = recover from hot memory
- `mid` = checkpoint the session
- `end` = close the session
- `deactivate <project-ref>` = remove a project from the active set
- `deactivate all` = clear all active project permissions for the conversation
- `disactivate ...` = tolerated alias for `deactivate ...`
- `audit framework` = run a manual framework audit for the primary active project

Do not treat plain `help`, `help all`, `init`, `init project`, `index projects`, `list projects`, `start`, `mid`, `end`, `activate`, `deactivate`, `disactivate`, `audit framework`, `Hot only`, `Check warm`, `Go cold`, `Deep recovery`, `Full recovery`, `Checkpoint`, or `Do not burn tokens` as permission to use this skill unless Session Logger was explicitly evoked.

For `help` and `help all`:

1. Read only `.agents/skills/session-logger/HELP.md`.
2. Print its contents verbatim.
3. Do not read `.agents/projects-index.json`.
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
- `start` recovers from hot memory only after init exists.
- `mid` writes a summary-only checkpoint.
- `end` writes closeout logs and may attempt scoped commit reconciliation.

If `activate` or `start` selects a project that has no workflow scaffold yet, instruct the user to run `init project <project-name>` instead of acting as if recovery can proceed.

## Project scope and initialization

`init project <project-name>` is the only Session Logger command meant for an uninitialized project.

For `init project`:

1. Confirm the user explicitly named the project.
2. Normalize the provided name: lowercase, trim, convert spaces/underscores to hyphens, remove unsupported characters, collapse repeated hyphens, and reject empty/colliding names with a concrete suggested alternative.
3. Create `projects/<normalized-name>/docs/workflow/` from the shared scaffold.
4. Register the project in `.agents/projects-index.json` with an immutable UUID v4 `id` and a `short_id` derived from the first UUID segment.
5. Create `projects/<normalized-name>/.codex/session-logger.toml` if the project does not already have framework audit state, and store the project ID when practical.
6. Set the adopted framework date to the current shared framework update date during explicit framework init.
7. Do not write real session memory during init.
8. Immediately activate the new project as primary so the user can start work.

For legacy `init <project-name>`:

- Treat it as `init project <project-name>` only when the intent is unambiguous.
- Prefer documenting and using `init project <project-name>` going forward.

For `index projects`:

1. Read and update only `.agents/projects-index.json`.
2. Inspect only immediate child directory names under `projects/`.
3. Check only whether Session Logger scaffold/config paths exist.
4. Never read project code, project memory file contents, archive contents, or recurse broadly through project trees.
5. Preserve existing project IDs and short IDs.
6. Assign a new UUID only when registering a newly discovered project, derive `short_id` from the first UUID segment, and regenerate if either the full ID or short ID collides.

For `list projects`:

- Read only `.agents/projects-index.json`.
- Show short ID first, plus full ID, canonical name, path, initialized/scaffold status, aliases, and last activation date.

For project references:

- Accept canonical name, alias, `short_id`, or full UUID `id`.
- Treat the full UUID `id` as the durable registry identity.
- Treat `short_id` as a convenience reference for user commands and lists.
- Do not silently choose a project if a reference is ambiguous; show the matching candidates and ask for a clearer project ref.

## Required project activation

Before live memory work:

1. Confirm the user explicitly activated a primary project by project ref, or selected one through `start` project selection.
2. Resolve writable Session Logger memory only under the primary project's `docs/workflow/`.
3. Treat projects after `+` in `activate <primary-ref> + <allowed-ref>, <allowed-ref>` as read-only.
4. Do not inspect any allowed read-only project unless the current user request explicitly needs it.
5. Never write Session Logger memory, checkpoints, framework upgrades, commits, or session-end logs to allowed read-only projects.

For `activate + <allowed-ref>, <allowed-ref>`:

1. Require an existing primary active project.
2. Keep the current primary unchanged.
3. Add the named projects as allowed read-only projects.
4. If no primary project is active, ask the user to activate a primary project first.

For `activate <project-ref>` when a different primary project is already active:

1. Pause before replacing the primary.
2. Ask whether the user intends to switch the primary project.
3. If the user confirms switching primary, activate the requested project as primary.
4. If the user says the intent was to add an allowed read-only project instead, add it as read-only and remind them that the explicit format is `$session-logger activate + <project-ref>`.

If no project is active and the command is `start`, read only `.agents/projects-index.json`, suggest the last three activated projects as `1`, `2`, `3`, or `Other`, and activate the selected project before recovery. If the user selects `Other`, list indexed projects excluding the recent three and ask for a project ref.

If no project is active for checkpoint, session-end, or audit work, ask the user to activate a primary project first.

See the current dogfooding release docs for the v1 context inventory and example packs:

- `projects/context-framework/docs/product/releases/2026-04/session_logger_context_inventory.md`
- `projects/context-framework/docs/product/releases/2026-04/session_logger_context_inventory_examples.md`
- `projects/context-framework/docs/product/releases/2026-04/effective_context_checklist.md`
- `projects/context-framework/docs/product/releases/2026-04/session_logger_activation_audit.md`

## Session topic

Logger commands are control text, never semantic topic text. Do not use `$session-logger start`, `Session Logger start`, or `Start session logger` as a chat/session/log/commit topic.

On activation/start:

1. If no primary project is active, read only `.agents/projects-index.json` and guide project selection before memory recovery.
2. Read hot memory only from the primary project: `project_identity.md` and `last_session_summary.md`.
3. Parse the date from the hot summary title when possible. Prefer `dd-mm-yyyy` from titles like `Mon, 14-04-2026 | ...`; also accept ISO `yyyy-mm-dd`.
4. If the hot summary is older than 72 hours, ask explicit permission to read `last_session_detailed.md`.
5. If warm access is granted, brief: where we are, what we were doing, what was done, and what is next. Then ask the user to choose the session topic.
6. If hot memory is fresh, warm access is denied, or the date is missing/unparseable, leave the topic pending and derive it from the first non-logger work request.

If a native runtime command exists to rename the chat/thread, use it only after a real topic is known. Otherwise use the derived topic in session logs, archive slugs, and commit messages.

## Startup

Within the primary active project only:

0. If no primary project is active, use `.agents/projects-index.json` to suggest recent projects with `1`, `2`, `3`, or `Other`; do not read project memory until a primary project is selected.
0. If the primary project's workflow scaffold does not exist yet, instruct the user to run `init project <project-name>`.
1. Read `project_identity.md`.
2. Read `last_session_summary.md`.
3. Apply the session-topic rules above.
4. Brief the current state from hot memory.
5. Do not escalate below hot without explicit permission.

## Permission model

These permissions apply only within the currently primary active project unless the user explicitly names an allowed read-only project and grants read depth for that project:

- `Hot only` = summary only
- `Check warm` = allow `last_session_detailed.md`
- `Go cold` = allow `sessions_history/`
- `Deep recovery` = allow warm + cold
- `Full recovery` = allow warm + cold + freezing
- `mid` = overwrite summary only
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

Use the Release C docs as the normative visibility surfaces:

- `effective_context_checklist.md` = short manual preflight review
- `session_logger_activation_audit.md` = compact live explanation block
- `session_logger_context_inventory.md` = deeper audit surface for included sources

The activation audit explains the current run. The context inventory remains the deeper explanation of included sources, permissions, freshness, and exclusions.

Do not treat the activation audit as permission to widen scope, bypass explicit invocation, or read warm/cold memory without approval.

## Manual framework audit

`audit framework` is manual-only for now. Do not add or imply automatic preflight checks.

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
7. Produce a framework audit report that states:
   - what changed since adoption
   - how those changes affect project structure, governed files, and governed data
   - what is already compliant
   - what is missing or outdated
   - what is unknown and needs clarification
   - which specific upgrade actions are proposed
8. Ask permission for specific explained actions before applying any change.

If the project has never adopted the framework formally, say so and propose initialization actions instead of pretending version comparison is possible.

Use audit result values:

- `up_to_date`
- `review_needed`
- `blocked`

Allowed action phrasing must be specific, such as:

- create `projects/<project-name>/.codex/session-logger.toml` with adopted framework date `YYYY-MM-DD`
- update `docs/workflow/session_start.md` to the current scaffold wording
- add missing governed workflow file `...`
- rewrite governed workflow sections to match the current framework procedure

Do not propose vague actions like `upgrade the project`, `sync framework`, or `make it compliant`.

## Native Codex commands

Use native commands as session-control helpers, not as memory sources:

- `/status` = inspect context/worktree/session state before checkpoint, session end, or a long next step.
- `/compact` = after checkpoint or session end when continuing with a smaller context.
- `/fork` = before risky exploration, alternate approaches, or parallel investigation that should not pollute the main session thread.

Before `/compact` or `/fork`, checkpoint first if useful state has not already been written to the primary active project's memory.

## Checkpoint

Within the primary active project only:

- Require both init and primary activation before `mid`.
- Use `/status` first when context pressure or worktree scope is unclear.
- Overwrite `last_session_summary.md`.
- Mark the header as a checkpoint.
- Do not archive.
- Do not write `last_session_detailed.md`.
- If continuing after a large context, suggest `/compact`.

## Session end

Within the primary active project only:

1. Require both init and primary activation before `end`.
2. Use `/status` first to confirm scope and unfinished work.
3. Archive previous latest-session files.
4. Write new `last_session_summary.md` and `last_session_detailed.md`.
5. Include grouped visible change state in the logs: `Committed`, `Remaining uncommitted`, `Skipped`, and `Failed`. Before the commit attempt, put visible dirty files under `Remaining uncommitted`.
6. After logging, check whether the primary active project or workspace is a git repository.
7. If scoped changes exist, request one-time permission for a commit transaction. Do not request a persistent approval rule.
8. Stage only primary-project changes plus explicitly allowed repo-level files. Never stage another project.
9. Commit with `type(scope): specific outcome`.
10. Rerun scoped status.
11. Rewrite only latest session logs, not archive copies, so committed files move from `Remaining uncommitted` to `Committed` and only actual leftovers remain dirty.
12. Stage the rewritten latest logs and amend the same commit.
13. If permission is denied/canceled, commit fails, or amend fails, do not claim success. Leave logs unchanged when no commit happened; if amend fails after commit, report the commit and the remaining dirty log rewrite accurately.
14. If continuing in the same thread after session end, suggest `/compact`.

## Commit message rules

Use concise conventional commit subjects:

```text
<type>(<scope>): <specific outcome>
```

- Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `build`, `ci`.
- Scope is the primary active project or subsystem, such as `meta-cli` or `session-logger`.
- Describe the actual work outcome, not the logging procedure.
- Avoid filler: `capture session end`, `update session log`, `misc changes`, `session cleanup`.
- For large sessions, keep the subject short and put grouped details in the commit body.

## Do not do this

- Do not treat `activate` or `start` as implicit bootstrap for an uninitialized project.
- Do not write to allowed read-only projects.
- Do not turn manual framework audit into an automatic preflight check.
- Do not read project memory before explicit project activation.
- Do not read warm/cold/freezing memory without permission.
- Do not read memory from any non-active project.
- Do not recover from framework docs/templates as if they were live state.
- Do not use logger commands as the session topic.
- Do not use `/fork` to bypass project access rules.
- Do not use `/compact` before writing needed checkpoint/session memory.
- Do not stage or commit another project without explicit authorization.
- Do not push to remote unless the user explicitly requests it.
- Do not invent missing work.
- Do not append to giant history logs.

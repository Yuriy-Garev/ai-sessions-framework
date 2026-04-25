# Session Logger Framework

A lightweight project-scoped session-memory framework for coding agents and fresh-chat recovery.

This directory is shared reference material. It is not live project memory.

## Design goals

- make restarts fast
- minimize token burn
- avoid unnecessary deep reads
- preserve factual continuity in one primary project at a time
- prevent implicit memory sharing across projects
- keep logger workflow commands separate from real session topics
- improve end-of-session data quality with scoped git reconciliation
- use native Codex session controls without weakening project isolation

## Core model

Each project that uses this framework should maintain its own workflow directory, usually:

```text
projects/<project-name>/docs/workflow/
```

The scaffold in `templates/workflow/` can be copied or adapted into an activated project after the user grants access to that project.

The repo-level project registry lives at:

```text
.agents/projects-index.json
```

It stores stable UUID project IDs, short convenience IDs, canonical names, scaffold status, and the last three activations. It is shared infrastructure, not live project memory.

The canonical printed command overview lives at:

```text
.agents/skills/session-logger/HELP.md
```

`help` and `help all` print that file verbatim and do not read project state.

Bootstrap for a new project should be treated as an explicit Session Logger operation:

```text
init project <project-name>
```

`init project` normalizes the project name, creates scaffold and placeholder files, registers the project, and immediately activates it as the primary project. It does not write real session memory.

Projects that want manual framework audit should also keep:

```text
projects/<project-name>/.codex/session-logger.toml
```

This file stores the project's adopted Session Logger framework date and last manual audit status. It is config, not workflow memory.

### Project-local procedure files
- `docs/workflow/session_start.md`
- `docs/workflow/session_end.md`
- `docs/workflow/checkpoint.md`

### Project-local static constants
- `docs/workflow/project_identity.md`

### Project-local memory files
- Hot: `docs/workflow/last_session_summary.md`
- Warm: `docs/workflow/last_session_detailed.md`

### Project-local archive folders
- Cold: `docs/workflow/sessions_history/`
- Freezing: `docs/workflow/sessions_history_detailed/`

## Source-of-truth priority

Within the primary active project only:

1. Current user request
2. `project_identity.md`
3. `last_session_summary.md`
4. Approved `last_session_detailed.md`
5. Approved files from `sessions_history/`
6. Approved files from `sessions_history_detailed/`

Older memory must never override the current user instruction. Memory from one project must never be used for another project.

## Access model

Project access is default-deny. Do not read or write any project-local workflow files until the user explicitly activates that project.

Session Logger supports one primary read/write project plus optional allowed read-only projects:

```text
activate <primary-ref> + <allowed-ref>, <allowed-ref>
activate + <allowed-ref>, <allowed-ref>
```

Project refs may be canonical names, aliases, short IDs, or full UUIDs from `.agents/projects-index.json`. The full UUID is the durable project identity; the short ID is a convenience reference for commands and lists.

The primary project is the only target for Session Logger memory writes, checkpoints, session-end logs, manual framework upgrades, and commits. Allowed projects are read-only and may be inspected only when explicitly relevant to the current user request. `activate + ...` adds read-only projects without changing the current primary.

If a primary project is already active and `activate <project-ref>` names a different project, pause before replacing the primary and ask whether the user intends to switch primary. If the user meant to add read-only access, add it as read-only and remind them that the explicit syntax is `$session-logger activate + <project-ref>`.

When a project is active, memory permissions apply only to the primary project unless the user explicitly names an allowed read-only project and grants read depth for that project.

If a project has not been initialized yet, `init project <project-name>` is the only valid Session Logger bootstrap step. `activate` and `start` should instruct the user to run `init project` first instead of pretending recovery can proceed.

## Command lifecycle

Use this lifecycle:

```text
help|index projects -> init project|activate -> audit framework -> start -> mid/end
```

- `help` prints the maintained help file without reading project memory, project index, or framework docs.
- `index projects` updates the repo-level registry from immediate project directory names plus scaffold/config existence only.
- `list projects` shows indexed projects, including short IDs and full IDs, without reading project memory.
- `init project` prepares, registers, and immediately activates an uninitialized project.
- `activate` chooses the primary read/write project and optional allowed read-only projects.
- `audit framework` manually compares the primary active project against the current shared framework methodology.
- `start` recovers from hot memory once the scaffold exists.
- `mid` writes a summary-only checkpoint.
- `end` writes closeout logs and may attempt scoped commit reconciliation.

## Session topic

Session Logger commands are control text. Do not use `$session-logger start`, `Session Logger start`, or `Start session logger` as the durable session topic.

On activation/start, read hot memory and parse the date from `last_session_summary.md` when possible. Prefer `dd-mm-yyyy` in titles like `Mon, 14-04-2026 | ...`; also accept ISO `yyyy-mm-dd`. If no project is active, `start` first reads only `.agents/projects-index.json` and offers recent projects as `1`, `2`, `3`, or `Other`. If the user selects `Other`, list indexed projects excluding the recent three and ask for a project ref. After a primary project is selected, apply normal hot recovery. If the hot summary is older than 72 hours, ask permission to read warm memory, brief the user on state, and ask them to choose the topic. If hot memory is fresh, warm access is denied, or the date cannot be parsed, derive the topic from the first non-logger work request.

## Permission phrases

- `Hot only`
- `Check warm`
- `Go cold`
- `Deep recovery`
- `Full recovery`
- `mid`
- `Do not burn tokens`

See the scaffolded operating files in `templates/workflow/` for exact behavior.

## Session Logger commands

The active skill is `session-logger`, displayed as Session Logger. It must be explicitly evoked before any skill action runs.

- `help` prints `.agents/skills/session-logger/HELP.md` verbatim.
- `help all` is an alias for `help`.
- `index projects` updates `.agents/projects-index.json` from immediate project folders and scaffold/config existence only.
- `list projects` displays indexed project metadata.
- `init project <project-name>` creates project-local workflow scaffold and placeholders, registers the project, and activates it as primary.
- `activate <project-ref>` selects the primary read/write project.
- `activate <primary-ref> + <allowed-ref>, <allowed-ref>` selects a primary read/write project plus allowed read-only projects.
- `activate + <allowed-ref>, <allowed-ref>` adds allowed read-only projects without changing the current primary.
- `deactivate <project-ref>` removes one project from the active set.
- `deactivate all` clears all active project permissions for the current conversation.
- `disactivate` is tolerated as an alias for `deactivate`, but `deactivate` is canonical.
- `audit framework` runs a manual framework audit for the primary active project only.
- `start` recovers from hot memory and applies topic freshness rules.
- `mid` writes a cheap mid-session checkpoint.
- `end` closes the session, writes history, attempts a one-time scoped git commit, and reconciles latest logs after a successful commit.

For the current dogfooding release, the v1 context inventory spec and example pack live in:

- `projects/context-framework/docs/product/releases/2026-04/session_logger_context_inventory.md`
- `projects/context-framework/docs/product/releases/2026-04/session_logger_context_inventory_examples.md`
- `projects/context-framework/docs/product/releases/2026-04/effective_context_checklist.md`
- `projects/context-framework/docs/product/releases/2026-04/session_logger_activation_audit.md`

These documents define how to describe included sources, permissions, freshness, and exclusion reasons for each Session Logger operation, plus how Session Logger should explain itself live while it is running.

## Release C Visibility Surfaces

Session Logger should be able to explain:

- why it activated
- what command it interpreted
- what rules constrained the run

Use these surfaces together:

- `effective_context_checklist.md` = manual operator preflight
- `session_logger_activation_audit.md` = live explanation surface
- `session_logger_context_inventory.md` = deeper audit surface

The activation audit is a compact runtime-facing markdown block. It does not replace the deeper context inventory.

Release C does not weaken existing safety boundaries:

- explicit invocation remains required
- scope stays primary-project-only for writes
- cross-project memory remains forbidden unless separately authorized as read-only
- warm/cold access remains permission-gated
- no auto-preflight or auto-permission behavior is added
- allowed projects remain read-only

## Manual framework audit

Session Logger supports a manual-only audit command:

```text
audit framework
```

This audit:

- requires explicit skill invocation
- requires one primary active project
- reads the primary active project's `.codex/session-logger.toml`
- reads shared update manifests from `updates/`
- compares the project's adopted framework date against the latest framework update date
- reads only newer framework update manifests after the adopted date
- evaluates only Session Logger-governed surfaces in the primary active project
- proposes specific approval-gated actions when changes are needed

This release does not add auto-update or preflight self-check behavior. Those ideas remain deferred.

## Governed project surfaces

Manual framework audit must stay inside Session Logger-governed project surfaces:

- `.codex/session-logger.toml`
- `docs/workflow/`
- project-local Session Logger procedure and scaffold files

Do not broaden the audit into unrelated application code or other project-specific areas.

## Framework update manifests

Shared framework updates live under:

```text
docs/frameworks/session-logger/updates/
```

Use `latest.md` as the current framework head and one dated file per framework update that may affect adopting projects.

Each update manifest should explain:

- what changed in the framework methodology
- why it changed
- impact on project structure
- impact on governed files
- impact on governed data
- required audit checks
- possible upgrade actions

## Context layer mapping

Treat Session Logger sources like this:

- `project_identity.md` = project-local identity context
- `last_session_summary.md` = hot state
- `last_session_detailed.md` = warm state
- `sessions_history/` = cold state
- `sessions_history_detailed/` = freezing state
- `docs/frameworks/session-logger/` = shared reference only, never live state
- `.agents/projects-index.json` = shared registry metadata, never live project memory
- `.agents/skills/session-logger/HELP.md` = printed command help, never live project memory

## End-of-session git flow

The `end` flow logs first, then tries to commit:

1. Write latest summary and detailed logs with grouped dirty state.
2. Inspect scoped git status.
3. If scoped changes exist, request one-time permission for a commit transaction.
4. Stage only active-project changes plus explicitly allowed repo-level files.
5. Commit using `type(scope): specific outcome`.
6. Rerun scoped status.
7. Rewrite latest logs only so committed files move to `Committed` and remaining dirty files stay under `Remaining uncommitted`.
8. Stage rewritten latest logs and amend the same commit.

If permission is denied/canceled, or no commit succeeds, leave logs as written. Never stage another project, and never push unless explicitly requested.

## Commit message rules

Use:

```text
<type>(<scope>): <specific outcome>
```

Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `build`, `ci`.

The subject must describe the actual work outcome, not logger mechanics. Avoid filler like `capture session end`, `update session log`, `misc changes`, and `session cleanup`.

## Native Codex commands

These commands are useful with the framework, but they do not replace project-local memory:

- `/status` before `mid`, `end`, or a long next step.
- `/compact` after `mid` or `end` when continuing with less context.
- `/fork` before risky exploration or alternate approaches.

Run `mid` before `/compact` or `/fork` when important state has not yet been written to the primary active project's memory.

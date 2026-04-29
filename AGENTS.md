# Repository Operating Instructions

> Claude Code reads `CLAUDE.md`, which is a thin pointer to this file. Behavior is identical for both runtimes; rules in this document govern Codex and Claude Code equally.

## Purpose
This repository is a reusable multi-project Codex workspace.

It has two layers:

- Repo-level shared infrastructure: root instructions, shared skills, shared framework docs, templates, and scaffolding.
- Contained working projects: actual implementation projects under `projects/`.

Do not collapse these layers. Framework guidance is shared; live project code and live project memory are project-local and permission-gated.

## Top-Level Structure
- `AGENTS.md` - routing and access rules for the whole workspace.
- `.codex/config.toml` - minimal repo-scoped Codex defaults.
- `.agents/skills/` - active shared skills only. Template scaffolding must live outside this directory.
- `.agents/projects-index.example.json` - tracked example schema for the local Session Logger project registry.
- `.agents/projects-index.json` - local ignored Session Logger project registry used for safe project selection.
- `.agents/templates/` - reusable agent/skill scaffolding that must not be discovered as active runtime behavior.
- `docs/frameworks/` - shared reusable framework documentation and reference material.
- `projects/` - actual implementation projects. Project subtrees are denied by default.

## Project Access: Default Deny
By default, Codex may inspect and edit only repo-level shared infrastructure.

Allowed by default:
- `AGENTS.md`
- `.codex/config.toml`
- `.agents/skills/`
- `.agents/projects-index.example.json`
- `.agents/projects-index.json` when it exists locally
- `.agents/templates/`
- `docs/frameworks/`
- root-level templates/scaffolding and non-project documentation

Not allowed by default:
- any path under `projects/`
- project code
- project-local session memory
- broad searches that traverse `projects/`
- cross-project comparison or discovery

Do not infer permission from the existence of a project directory. A project may be accessed only when the user explicitly names and allows that project.

Exception: when Session Logger is explicitly evoked with `index projects`, Codex may scan only immediate child directory names under `projects/` and check for the existence of Session Logger scaffold/config paths. It must not read project code, project memory file contents, archive contents, or recurse broadly through project trees.

## Project Activation
Project access is conversation-local and activated only by an explicit user instruction such as:

```text
activate <project-ref>
```

Session Logger also supports multi-project activation syntax:

```text
activate <primary-project-ref> + <allowed-project-ref>, <allowed-project-ref>
activate + <allowed-project-ref>, <allowed-project-ref>
```

Project refs may be canonical names, aliases, short IDs, or full UUIDs from the local `.agents/projects-index.json`. The full UUID is the durable project ID; the short ID is a convenience reference.

Project records in the local `.agents/projects-index.json` carry an access policy. Normal writable projects use `access_mode: "managed"`. External cloned reference repositories use `access_mode: "external_read_only"` and `write_policy: "forbidden"`.

The primary project is the only read/write Session Logger project. Allowed projects after `+` are read-only and may be inspected only when explicitly relevant to the current request. `activate + ...` adds read-only projects without changing the current primary.

External read-only projects are never valid primary projects. They may be listed, resolved by name/short ID/full UUID, and added only as allowed read-only references with `activate + <project-ref>` after a writable managed primary is active. Session Logger must not run `init project`, `mid`, `end`, automatic safety entries, framework upgrades, scaffold writes, memory writes, session-end logs, commits, or any other write action against an external read-only project.

If a primary project is already active and `activate <project-ref>` names a different project, pause before replacing the primary and ask whether the user intends to switch primary. If the user meant to add read-only access, add it as read-only and remind them that the explicit syntax is `activate + <project-ref>`.

After primary activation, Codex may access:
- shared repo infrastructure
- `projects/<primary-project>/`
- the primary project's local session-memory files, subject to the permission phrases below

Allowed read-only projects do not receive Session Logger writes, checkpoints, session-end logs, framework upgrades, or commits. Automatic safety entries also write only to the primary project.

Cross-project work is forbidden unless the user explicitly authorizes all named projects involved, for example:

```text
Allow projects: project_a, project_b for comparison
```

## Project-Scoped Session Memory
Live session memory must be stored inside the active project. There must never be one shared live memory pool for all projects.

Recommended convention for each project:

```text
projects/<project-name>/docs/workflow/
  project_identity.md
  last_session_summary.md
  auto_recovery.md
  last_session_detailed.md
  sessions_history/
  sessions_history_detailed/
```

`auto_recovery.md` is active hot-adjacent workflow memory. It is an append-only buffer for automatic safety entries during an active session, not archive history and not warm/cold/freezing memory.

`docs/frameworks/session-logger/` contains reusable reference docs and templates only. It is not live project memory.

## Mandatory Startup Behavior
Before doing substantive work in an activated project:

1. Confirm the active project was explicitly named by the user.
2. Read only that project's `docs/workflow/project_identity.md`.
3. Read only that project's `docs/workflow/last_session_summary.md`.
4. Recover from hot memory first.
5. Do not read deeper memory unless the user explicitly authorizes it.

If no project is active, do not read project memory. For repo-level infrastructure tasks, use only repo-level shared files.

## Permission Phrases
Interpret these phrases only within the currently active and allowed project:

- `Hot only` -> do not read beyond that project's `last_session_summary.md`
- `Check warm` -> may read that project's `last_session_detailed.md`
- `Go cold` -> may open files in that project's `docs/workflow/sessions_history/`
- `Deep recovery` -> may read that project's warm + cold memory
- `Full recovery` -> may read that project's warm + cold + freezing memory
- `mid` -> manually update that project's `last_session_summary.md` only and mark it as a checkpoint
- `Do not burn tokens` -> keep recovery hot-only and responses compact

These phrases never grant access to another project.

For multi-project sessions, permission phrases apply only to the primary project unless the user explicitly names an allowed read-only project and grants a read depth for that project. Allowed projects remain read-only even when deeper read permission is granted.

## Source-of-Truth Priority
For repo-level infrastructure work:

1. Current user request
2. `AGENTS.md`
3. Relevant shared skills or framework docs
4. Relevant root-level configuration or documentation

For activated project work:

1. Current user request
2. Active project's `docs/workflow/project_identity.md`
3. Active project's `docs/workflow/last_session_summary.md`
4. Active project's `docs/workflow/auto_recovery.md` when manual `mid`, `end`, or unclosed-plan recovery needs pending automatic entries
5. Approved active-project `docs/workflow/last_session_detailed.md`
6. Approved active-project files in `docs/workflow/sessions_history/`
7. Approved active-project files in `docs/workflow/sessions_history_detailed/`

Older memory must never override the current user request. Memory from one project must never be used for another project.

## Session Operations
Use the `session-logger` skill only when the user explicitly evokes Session Logger, for example with `$session-logger`, `Session Logger`, or a direct link to that skill. It writes only to the primary active project's local workflow files.

When explicitly evoked, Session Logger commands are:
- `help` / `help all` -> print `.agents/skills/session-logger/HELP.md` without reading project state
- `index projects` -> create or update the local `.agents/projects-index.json` from immediate project directory names and scaffold/config existence only
- `list projects` -> list indexed projects, showing short IDs and full IDs, without reading project memory
- `init project <project-name>` -> create a normalized project scaffold, register it, and activate it as primary
- `activate <project-ref>` -> activate that project as primary
- `activate <primary-ref> + <allowed-ref>, <allowed-ref>` -> activate a primary read/write project plus allowed read-only projects
- `activate + <allowed-ref>, <allowed-ref>` -> add allowed read-only projects without changing the current primary
- `start` -> recover from hot memory after activation, or guide project selection from the local `.agents/projects-index.json` if no project is active
- `mid` -> checkpoint the session
- `end` -> close the session
- `deactivate <project-ref>` / `deactivate all` -> remove project access for the current conversation

Do not run Session Logger actions from plain `start`, `mid`, `end`, or `activate` unless the skill was explicitly evoked in the same request. The only exceptions are automatic safety entries, defined below.

If no project is active, Session Logger `start` may read only the local `.agents/projects-index.json` and offer the last three activated projects as `1`, `2`, `3`, or `Other`. It must activate the chosen project before reading or writing live session memory.

The public repository tracks `.agents/projects-index.example.json` as the schema example. The live `.agents/projects-index.json` is local ignored state and may be created by `index projects` or `init project`.

If a suggested or selected project has `access_mode: "external_read_only"`, it cannot be selected as primary for `start`; ask the user to activate a managed primary and then add the external project with `activate + <project-ref>` if read-only reference access is needed.

## Automatic Safety Entries
When a primary project is active and the user accepts a project-scoped implementation plan that mutates project or session-scoped state, Codex must append a Session Logger automatic safety entry before the first mutating execution step and another after execution and verification, before the final response, `/compact`, or `/fork`.

Automatic safety entries are a narrow exception to the explicit invocation requirement. They exist to preserve the BEFORE -> NOW delta during long, compacted, or multi-plan work without overwriting hot summary memory.

They apply to every distinct accepted project-scoped execution plan that will:
- edit files
- run migrations, code generation, or formatters that write files
- stage, commit, or otherwise mutate repository state
- perform any side-effectful implementation action

They also apply to these narrow non-plan safety events when useful state has not already been captured:
- context pressure
- before `/compact`
- before `/fork`
- major decision or milestone recognized by the agent

They do not apply to read-only exploration, planning-only turns, or non-mutating checks.

Root shared-infrastructure work may proceed without a primary project. Do not create root-level shared memory for root-only work, and do not write automatic safety entries unless a primary project is active and the work is being captured for that project.

Before appending any automatic safety entry, Codex must confirm primary activation, project initialization, and current-thread hot recovery. If hot recovery has not happened in the current thread, read only the primary project's `project_identity.md` and `last_session_summary.md` first. If required files are missing or unreadable, block execution and report the missing prerequisite.

Automatic safety entries write only to the primary project's `docs/workflow/auto_recovery.md`. They append and never overwrite existing content. If `auto_recovery.md` is missing in an initialized project, manual framework audit should propose adding it from the scaffold; automatic execution must not silently create project-local scaffold files outside an adopted framework update.

Before-plan checkpoints must capture:
- timestamp
- source or trigger
- stable plan/checkpoint ID
- prior state
- intended plan
- decisions and assumptions
- expected files or scope
- known risks

After-plan checkpoints must capture:
- timestamp
- source or trigger
- the same plan/checkpoint ID
- original intent summary
- actual changes
- decisions made during execution
- verification performed
- remaining work
- the BEFORE -> NOW delta

If a before-plan entry has no matching after-plan entry, later `start`, manual `mid`, or `end` must surface it as an unclosed plan marker.

Automatic safety entries obey all normal Session Logger write boundaries:
- require an already active primary project
- write only the primary project's `docs/workflow/auto_recovery.md`
- never write to allowed read-only projects
- do not archive
- do not write `last_session_detailed.md`
- do not imply warm, cold, or freezing memory access

If no primary project is active for project-scoped work that requires automatic safety capture, Codex must block plan execution and ask the user to activate or start a primary project. If an automatic safety entry fails, Codex must block execution or final delivery as appropriate and report the failure. When an automatic non-plan safety entry is appended, briefly tell the user what was captured and why.

## Skills and Frameworks
- Prefer active repo-local skills in `.agents/skills/` when the task matches a skill description.
- Shared reusable frameworks live in `docs/frameworks/`.
- Load framework docs only when the task clearly needs them.
- Do not treat framework reference docs or templates as live project state.

## Guardrails
- Do not invent tasks, blockers, progress, artifacts, or decisions.
- Do not access `projects/` without explicit project authorization.
- Do not run broad repo-wide searches that traverse `projects/` unless the user explicitly authorizes the relevant project scope.
- Do not open archive contents without the matching project-level permission phrase.
- Preserve archive naming rules defined by the session framework.
- If a repo-specific build, test, or lint command exists, run only checks that respect the currently allowed scope before declaring work done.

## Git Boundaries
- Treat every `projects/<project-name>/` directory as an independent git repository.
- If the workspace root is versioned, use root git only for shared infrastructure such as `AGENTS.md`, `.codex/`, `.agents/`, `docs/`, and root-level scaffolding.
- Do not interpret root `git status` as project status.
- For an activated project, run git commands from `projects/<project-name>/` or with `git -C projects/<project-name> ...`.
- Do not commit project files from the workspace root.
- Do not use git submodules for projects unless the user explicitly chooses that model later.

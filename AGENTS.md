# Repository Operating Instructions

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
- `.agents/projects-index.json` - repo-level Session Logger project registry used for safe project selection.
- `.agents/templates/` - reusable agent/skill scaffolding that must not be discovered as active runtime behavior.
- `docs/frameworks/` - shared reusable framework documentation and reference material.
- `projects/` - actual implementation projects. Project subtrees are denied by default.

## Project Access: Default Deny
By default, Codex may inspect and edit only repo-level shared infrastructure.

Allowed by default:
- `AGENTS.md`
- `.codex/config.toml`
- `.agents/skills/`
- `.agents/projects-index.json`
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

Project refs may be canonical names, aliases, short IDs, or full UUIDs from `.agents/projects-index.json`. The full UUID is the durable project ID; the short ID is a convenience reference.

The primary project is the only read/write Session Logger project. Allowed projects after `+` are read-only and may be inspected only when explicitly relevant to the current request. `activate + ...` adds read-only projects without changing the current primary.

If a primary project is already active and `activate <project-ref>` names a different project, pause before replacing the primary and ask whether the user intends to switch primary. If the user meant to add read-only access, add it as read-only and remind them that the explicit syntax is `activate + <project-ref>`.

After primary activation, Codex may access:
- shared repo infrastructure
- `projects/<primary-project>/`
- the primary project's local session-memory files, subject to the permission phrases below

Allowed read-only projects do not receive Session Logger writes, checkpoints, session-end logs, framework upgrades, or commits.

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
  last_session_detailed.md
  sessions_history/
  sessions_history_detailed/
```

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
- `mid` -> update that project's `last_session_summary.md` only and mark it as a checkpoint
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
4. Approved active-project `docs/workflow/last_session_detailed.md`
5. Approved active-project files in `docs/workflow/sessions_history/`
6. Approved active-project files in `docs/workflow/sessions_history_detailed/`

Older memory must never override the current user request. Memory from one project must never be used for another project.

## Session Operations
Use the `session-logger` skill only when the user explicitly evokes Session Logger, for example with `$session-logger`, `Session Logger`, or a direct link to that skill. It writes only to the primary active project's local workflow files.

When explicitly evoked, Session Logger commands are:
- `help` / `help all` -> print `.agents/skills/session-logger/HELP.md` without reading project state
- `index projects` -> update `.agents/projects-index.json` from immediate project directory names and scaffold/config existence only
- `list projects` -> list indexed projects, showing short IDs and full IDs, without reading project memory
- `init project <project-name>` -> create a normalized project scaffold, register it, and activate it as primary
- `activate <project-ref>` -> activate that project as primary
- `activate <primary-ref> + <allowed-ref>, <allowed-ref>` -> activate a primary read/write project plus allowed read-only projects
- `activate + <allowed-ref>, <allowed-ref>` -> add allowed read-only projects without changing the current primary
- `start` -> recover from hot memory after activation, or guide project selection from `.agents/projects-index.json` if no project is active
- `mid` -> checkpoint the session
- `end` -> close the session
- `deactivate <project-ref>` / `deactivate all` -> remove project access for the current conversation

Do not run Session Logger actions from plain `start`, `mid`, `end`, or `activate` unless the skill was explicitly evoked in the same request.

If no project is active, Session Logger `start` may read only `.agents/projects-index.json` and offer the last three activated projects as `1`, `2`, `3`, or `Other`. It must activate the chosen project before reading or writing live session memory.

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

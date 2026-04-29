# Codex + Claude Workspace Template

This repository is a forkable multi-project workspace template for Codex and Claude Code.

It keeps shared agent instructions, skills, framework docs, and scaffolding in the root repository while keeping actual implementation projects under `projects/` as local, independently versioned workspaces.

## Quickstart

1. Fork or clone this repository.
2. Open it with Codex or Claude Code.
3. Read `AGENTS.md` before touching `.agents/`, `docs/`, or `projects/`.
4. For Claude Code, `CLAUDE.md` points back to the same rules so both runtimes behave consistently.
5. Initialize or index local projects with Session Logger:

```text
$session-logger index projects
$session-logger init project my-project
$session-logger start
```

## Repository Layout

- `AGENTS.md` - canonical workspace rules for Codex and Claude.
- `CLAUDE.md` - Claude Code entrypoint that points to the canonical rules.
- `.codex/config.toml` - repo-scoped Codex defaults.
- `.claude/settings.json` - portable Claude Code allowlist for common read-only inspection commands.
- `.claude/skills/` - Claude discovery shims that point to canonical skills.
- `.agents/skills/` - canonical shared skills.
- `.agents/templates/` - reusable scaffolding that must not be discovered as active skills.
- `docs/frameworks/` - reusable framework documentation and workflow templates.
- `projects/` - local implementation projects; denied by default and independently versioned.

## Local State

The live Session Logger registry is local state:

```text
.agents/projects-index.json
```

It is intentionally ignored by git because it contains fork-specific project names, paths, IDs, access policy, and recent activation history. Use `.agents/projects-index.example.json` as the tracked schema example.

Claude machine-local permission overrides belong in:

```text
.claude/settings.local.json
```

That file is also ignored because it may contain local paths or personal tool allow rules.

## Session Logger

Session Logger is a project-scoped memory workflow for fresh-chat recovery, checkpoints, and session closeout.

Key rules:

- It activates only when explicitly invoked with `$session-logger`, `Session Logger`, or a direct skill link.
- It reads no project memory until a project is explicitly activated or selected through `start`.
- It writes memory only to the primary active project.
- Allowed projects after `+` are read-only references.
- The live registry can be recreated or refreshed with `$session-logger index projects`.

The canonical skill lives at `.agents/skills/session-logger/SKILL.md`; the framework docs live at `docs/frameworks/session-logger/`.

## Fork Workflow

Keep reusable instructions, skills, and framework improvements in this root repository. Keep real application repositories under `projects/`, where each project can have its own git history.

Before publishing changes from a fork, check that local state is still ignored:

```text
git check-ignore -v .agents/projects-index.json .claude/settings.local.json
git ls-files .agents/projects-index.json .claude/settings.local.json
```

The first command should report `.gitignore`; the second should print nothing.

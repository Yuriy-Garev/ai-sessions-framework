# CodexAccess — Claude Code Entry

This is a Codex-first multi-project workspace. Claude Code runs here with **identical behavior** to Codex. There is no Claude-specific framework.

## Source of truth

Read [`AGENTS.md`](AGENTS.md) in full before any action that touches `.agents/`, `docs/`, or `projects/`. All access rules, project activation, Session Logger lifecycle, automatic safety entries, permission phrases, and git boundaries are defined there. Do not duplicate them here.

## Claude-specific rules

1. **Auto-memory is disabled in this repo.** Do not create or write to `~/.claude/projects/D--Desktop-CodexAccess/memory/**` for any work scoped to this workspace. The Session Logger framework (project-local `docs/workflow/`) is the only memory system. If you encounter pre-existing auto-memory entries from earlier sessions, treat them as read-only legacy and prefer Session Logger memory.

2. **Default-deny on `projects/**`.** Do not read project code, project memory, or recurse into a project subtree until the user explicitly says `activate <project-ref>` (or one of the `activate ... + ...` forms). This mirrors AGENTS.md exactly.

3. **Each `projects/<name>/` is an independent git repo.** Run git commands from inside that directory or with `git -C projects/<name> ...`. Never commit project files from the workspace root.

4. **Session Logger is invocation-gated.** Do nothing on plain `start`, `mid`, `end`, `activate`, `help`, `init`, `index projects`, etc. Run Session Logger actions only after explicit evocation: `$session-logger`, `Session Logger`, or a direct skill link. The single exception is automatic safety entries, which fire only when a primary project is already active and an accepted implementation plan is about to mutate or has just finished mutating state — see AGENTS.md §"Automatic Safety Entries" and the canonical SKILL.md §"Checkpoint".

5. **Skills are canonical at `.agents/skills/`.** The `.claude/skills/<name>/SKILL.md` pointer files exist only to register the skill with Claude Code's matcher. When a skill triggers, read the canonical `.agents/skills/<name>/SKILL.md` before acting.

## Available skills

- `session-logger` — project-scoped session memory; canonical at [`.agents/skills/session-logger/SKILL.md`](.agents/skills/session-logger/SKILL.md). Help text at [`.agents/skills/session-logger/HELP.md`](.agents/skills/session-logger/HELP.md).
- `cli-docs-creator` — CLI help text design and review; canonical at [`.agents/skills/cli-docs-creator/SKILL.md`](.agents/skills/cli-docs-creator/SKILL.md).

## Repo-level shared infrastructure (always allowed)

- `AGENTS.md`, `CLAUDE.md` (this file)
- `.codex/config.toml`, `.claude/settings.json`
- `.agents/skills/`, `.agents/projects-index.json`, `.agents/templates/`
- `docs/frameworks/`
- root-level templates and non-project documentation

Anything under `projects/` requires explicit activation.

## Frameworks

- Session Logger framework reference: [`docs/frameworks/session-logger/`](docs/frameworks/session-logger/) (shared reference, not live state).
- Latest framework manifest: [`docs/frameworks/session-logger/updates/latest.md`](docs/frameworks/session-logger/updates/latest.md).

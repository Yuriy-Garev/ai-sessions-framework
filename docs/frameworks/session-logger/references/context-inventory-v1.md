# Session Logger Context Inventory v1

This shared reference defines the auditable context package format for Session Logger operations.

It is documentation-first. It does not require an automatically generated artifact under project workflow memory.

## Purpose

The context inventory answers:

- what sources were included
- which project scope they belonged to
- which project was primary and which projects were allowed read-only
- whether registry metadata was used
- which permission level allowed them
- how fresh they were
- why they were included
- what they were used for
- roughly how much context they cost

## Canonical Format

```yaml
context_operation: help|index projects|list projects|init project|activate|start|mid|end|deactivate|audit framework|automatic safety entry
primary_active_project: <project-name-or-null>
primary_active_project_short_id: <short-id-or-null>
allowed_read_only_projects: []
allowed_read_only_project_short_ids: []
project_index_used: true|false
topic_status: pending|known|not_applicable
total_token_estimate: <approx-integer>
items:
  - source: <file-or-surface>
    scope: primary_active_project|allowed_read_only_project|project_registry|shared_repo_reference
    permission_level: reference|hot|auto_recovery|warm|cold|freezing|not_applicable
    freshness: static|current|stale|archived|reference|not_applicable
    inclusion_reason: <short factual reason>
    token_estimate: <approx-integer>
    used_for: <short factual purpose>
```

## Source Mapping

| Source | Context layer | Scope | Permission level | Notes |
|---|---|---|---|---|
| `project_identity.md` | project-local identity context | `primary_active_project` | `hot` | Static constants for the primary project |
| `last_session_summary.md` | hot state | `primary_active_project` | `hot` | First recovery surface |
| `auto_recovery.md` | active automatic recovery buffer | `primary_active_project` | `auto_recovery` | Append-only automatic safety entries; not archive or warm memory |
| `last_session_detailed.md` | warm state | `primary_active_project` | `warm` | Only after explicit permission |
| `sessions_history/` | cold state | `primary_active_project` | `cold` | Archive content only after explicit permission |
| `sessions_history_detailed/` | freezing state | `primary_active_project` | `freezing` | Deep archive content only after explicit permission |
| `docs/frameworks/session-logger/` | shared reference only | `shared_repo_reference` | `reference` | Never live project memory |
| `.agents/projects-index.json` | project registry metadata | `project_registry` | `not_applicable` | Safe project selection surface, never live project memory |
| `.agents/skills/session-logger/HELP.md` | command help text | `shared_repo_reference` | `reference` | Printed verbatim for help, never live project memory |

## Operation Notes

- `help` prints `.agents/skills/session-logger/HELP.md` verbatim and must not read project state.
- `index projects` updates `.agents/projects-index.json` from immediate project child names and scaffold/config existence only.
- `list projects` reads only `.agents/projects-index.json`.
- `init project` uses shared scaffold inputs, writes project workflow placeholders, registers the project, and activates it as primary without recovering live memory.
- `activate` chooses a primary read/write project and optional allowed read-only projects.
- `start` recovers from `project_identity.md` and `last_session_summary.md`; it may surface unresolved `auto_recovery.md` entries without granting warm/cold access.
- manual `mid` blends project identity, hot summary, current context, and relevant `auto_recovery.md` entries into a curated hot-summary checkpoint.
- automatic safety entries append only to `auto_recovery.md`.
- `end` writes latest logs, merges useful `auto_recovery.md` entries, clears the buffer only after successful incorporation, and then runs scoped git reconciliation when appropriate.

## Scope Rules

- `scope` may be `primary_active_project`, `allowed_read_only_project`, `project_registry`, or `shared_repo_reference`.
- Session Logger must never include another project unless it is explicitly activated as primary or allowed read-only.
- Permission phrases widen depth only inside the primary active project unless an allowed read-only project is explicitly named and permitted.
- Allowed read-only projects must never receive Session Logger writes.

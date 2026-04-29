# Session Start Procedure

This file is scaffold material. When copied into a project, it operates only on that project's local `docs/workflow/` directory.

This procedure assumes the project has already been bootstrapped with `init project <project-name>`. If the workflow scaffold is missing, tell the user to run `init project` first.

## Required Actions
1. Confirm the user explicitly activated this project as the primary project, or selected it through registry-assisted `start`.
2. If no primary project is active, read only the local `.agents/projects-index.json`.
3. Suggest the last three activated projects using question mode when possible: `1`, `2`, `3`, or `Other`.
4. If the user selects `Other`, list indexed projects excluding the recent three and ask for a project ref.
5. Activate the selected project as primary before reading project memory.
6. If `docs/workflow/` is missing or placeholder files were never scaffolded, stop and instruct the user to run `init project <project-name>`.
7. Read this project's `docs/workflow/project_identity.md`.
8. Read this project's `docs/workflow/last_session_summary.md`.
9. If `docs/workflow/auto_recovery.md` exists, inspect it only for unclosed automatic plan markers and unresolved safety entries that affect restart accuracy.
10. Parse the date from the hot summary title when possible. Prefer `dd-mm-yyyy` from titles like `Mon, 14-04-2026 | ...`; also accept ISO `yyyy-mm-dd`.
11. If hot memory is older than 72 hours, ask explicit permission before reading `last_session_detailed.md`.
12. If warm access is granted, brief:
   - where we are,
   - what we were doing,
   - what was done,
   - what is next.
13. Ask the user to choose the session topic after stale warm recovery.
14. If hot memory is fresh, warm access is denied, or the date is missing/unparseable, keep the topic pending until the first non-logger work request.
15. State any uncertainty from hot-only recovery, including any unclosed automatic plan markers.

---

## Session Topic
Logger commands are control text, not semantic topic text.

Do not use these as session titles, archive slugs, commit subjects, or chat names:
- `$session-logger start`
- `Session Logger start`
- `Start session logger`
- other logger-only command phrases

If a native runtime command exists to rename the chat/thread, use it only after a real topic is known. Otherwise use the derived topic in session logs, archive names, and commits.

---

## Default Access
| Source | Allowed |
|---|---|
| `project_identity.md` | yes, within active project |
| `last_session_summary.md` | yes, within active project |
| `auto_recovery.md` | yes, unresolved automatic entries only |
| `last_session_detailed.md` | no, except explicit stale-hot warm permission |
| archive filenames | yes, within active project |
| archive file contents | no |
| `.agents/projects-index.json` | yes, only before activation or for project listing; local ignored registry state |

---

## Truth Priority
1. Current user request
2. Primary active project `project_identity.md`
3. Primary active project `last_session_summary.md`
4. Primary active project `auto_recovery.md` for unresolved automatic entries
5. Approved deeper sources from the primary active project only

Older memory never overrides current user instruction. Memory from another project is never in scope unless that project is explicitly activated as read-only and explicitly relevant to the current request.

---

## Multi-Project Access

Use:

```text
activate <primary-ref> + <allowed-ref>, <allowed-ref>
activate + <allowed-ref>, <allowed-ref>
```

- The primary project is read/write for Session Logger workflow memory.
- Allowed projects are read-only.
- `activate + ...` adds allowed read-only projects without changing the current primary.
- Project refs may be names, aliases, short IDs, or full UUIDs from the local `.agents/projects-index.json`.
- Allowed projects must not receive checkpoints, session-end logs, framework upgrades, commits, or memory writes.

---

## Must-Not-Do
- Do not read project memory before explicit project activation.
- Do not write to allowed read-only projects.
- Do not read warm / cold / freezing without permission.
- Do not treat `auto_recovery.md` as warm, cold, freezing, or archive memory.
- Do not read another project's memory.
- Do not use logger command text as the session topic.
- Do not invent tasks, blockers, or progress.
- Do not treat this file as session memory.
- Do not silently burn tokens on deeper recovery.

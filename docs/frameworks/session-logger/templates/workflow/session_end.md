# Session End Procedure

This file is scaffold material. When copied into a project, it operates only on that project's local `docs/workflow/` directory.

This procedure assumes the project has already been bootstrapped with `init project <project-name>` and then activated as primary.

## Required Actions
1. Confirm the user explicitly activated this project.
2. If the workflow scaffold is missing, stop and instruct the user to run `init project <project-name>`.
3. Use `/status` first to confirm scope, context pressure, and unfinished work.
4. Archive this project's `docs/workflow/last_session_summary.md` to `docs/workflow/sessions_history/`.
5. Archive this project's `docs/workflow/last_session_detailed.md` to `docs/workflow/sessions_history_detailed/`.
6. Write new `docs/workflow/last_session_summary.md`.
7. Write new `docs/workflow/last_session_detailed.md`.
8. Include grouped visible change state in the latest logs:
   - `Committed`
   - `Remaining uncommitted`
   - `Skipped`
   - `Failed`
9. Before the commit attempt, put visible dirty files under `Remaining uncommitted`.
10. After all session logging actions complete, check whether the primary active project or workspace is under git.
11. If git is present, inspect uncommitted changes with a scoped status check that respects the primary active project boundary.
12. If scoped changes exist, request one-time permission for a commit transaction. Do not request a persistent approval rule.
13. Stage only active-project changes plus explicitly allowed repo-level files. Never stage another project.
14. Commit using `type(scope): specific outcome`.
15. Rerun scoped status.
16. Rewrite latest session logs only, not archive copies, so committed files move from `Remaining uncommitted` to `Committed` and only actual leftovers remain dirty.
17. Stage the rewritten latest logs and amend the same commit.
18. Confirm restart state is ready.
19. If continuing in the same thread, suggest `/compact`.

Archive before writing new files. Never overwrite first.

---

## Archive Naming
`YYYY-MM-DD__session-goal-summary.md`
`YYYY-MM-DD__session-goal-detailed.md`

Slug: lowercase, words separated by `-`, no spaces, no punctuation except `-` and `_`.

---

## Summary File Must Contain
- Title
- TL;DR
- Context: WHY / HOW / WHAT
- Current State
- Next Recommended Step
- Key Decisions Already Made
- Decisions This Session
- Important References
- Git State, if git is present
- Risks / Watchouts

Omit any section that is empty.

---

## Detailed File Must Contain
- Title
- Context: WHY / HOW / WHAT
- Summary
- Tasks: Completed / Unfinished / Not Started
- Main Accomplishments
- Git State, if git is present
- Artifacts, only if real
- Updated Project Structure, only if structure changed

Omit sections that have no content.

---

## Git State Format

Use concise grouped status:

```text
## Git State

### Committed
- [hash or pending] path/or/group - what changed

### Remaining uncommitted
- M path - why it remains

### Skipped
- path - outside active scope / intentionally not staged / not worth committing

### Failed
- action - error or reason
```

Before the commit attempt, all visible dirty files belong under `Remaining uncommitted`. After a successful commit and amend, rewrite latest logs so only actual leftovers remain there.

---

## Commit Transaction

Run this after session logging is complete if the repository uses git and scoped changes exist.

1. Request one-time permission to stage, commit, rewrite latest logs, stage rewritten latest logs, and amend the same commit.
2. Run a scoped status check that does not inspect unauthorized projects.
3. Stage only active-project changes plus explicitly allowed repo-level files.
4. Commit using the commit message rules below.
5. Rerun scoped status.
6. Rewrite only `last_session_summary.md` and `last_session_detailed.md` to reconcile `Committed`, `Remaining uncommitted`, `Skipped`, and `Failed`.
7. Stage the rewritten latest logs.
8. Amend the same commit.
9. Do not push unless the user explicitly requests it.

If permission is denied/canceled, commit fails, or amend fails, do not pretend success. Preserve or report the remaining dirty state accurately.

---

## Commit Message Rules

Format:

```text
<type>(<scope>): <specific outcome>
```

Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `build`, `ci`.

Rules:
- Scope is the primary active project or subsystem, such as `meta-cli` or `session-logger`.
- Subject describes the actual work outcome, not the logging procedure.
- Avoid filler: `capture session end`, `update session log`, `misc changes`, `session cleanup`.
- For large sessions, keep the subject short and put grouped details in the body.

---

## Completion Check
1. Can a fresh chat resume correctly from this project's `last_session_summary.md` alone? If not, improve it.
2. `last_session_detailed.md` has enough fidelity for warm recovery.
3. Previous files were archived before being replaced.
4. Archive filenames are OS-safe and date-prefixed.
5. If a commit succeeded, latest logs reflect only remaining uncommitted state after the commit/amend.

---

## Must-Not-Do
- Do not end a session before explicit project activation.
- Do not write outside the primary active project's workflow directory.
- Do not append to a giant history log.
- Do not use unsafe filenames.
- Do not write empty placeholder sections.
- Do not overwrite latest-session files before archiving.
- Do not use `/compact` before writing the final session memory.
- Do not use `/fork` to bypass project access rules.
- Do not stage or commit another project without explicit authorization.
- Do not push to remote unless the user explicitly requests it.

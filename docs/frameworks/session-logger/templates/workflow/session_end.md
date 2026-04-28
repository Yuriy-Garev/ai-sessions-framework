# Session End Procedure

This file is scaffold material. When copied into a project, it operates only on that project's local `docs/workflow/` directory.

This procedure assumes the project has already been bootstrapped with `init project <project-name>` and then activated as primary.

## Required Actions
1. Confirm the user explicitly activated this project.
2. If the workflow scaffold is missing, stop and instruct the user to run `init project <project-name>`.
3. Use `/status` first to confirm scope, context pressure, and unfinished work.
4. Archive this project's `docs/workflow/last_session_summary.md` to `docs/workflow/sessions_history/`.
5. Archive this project's `docs/workflow/last_session_detailed.md` to `docs/workflow/sessions_history_detailed/`.
6. Read `docs/workflow/auto_recovery.md` if it exists.
7. Merge useful automatic entries into the new summary and detailed logs, preserving decisions, context, risks, relevant paths, and unclosed plan markers.
8. Remove duplicates against existing hot/warm session facts.
9. Write new `docs/workflow/last_session_summary.md`.
10. Write new `docs/workflow/last_session_detailed.md`.
11. Clear `docs/workflow/auto_recovery.md` only after confirming useful entries were incorporated into the new latest-session memory. If merge or write fails, leave `auto_recovery.md` untouched.
12. Include grouped visible change state in the latest logs:
   - `Committed`
   - `Remaining uncommitted`
   - `Skipped`
   - `Failed`
13. Before the commit attempt, put visible dirty files under `Remaining uncommitted`.
14. After all session logging actions complete, check whether the primary active project or workspace is under git.
15. If git is present, inspect uncommitted changes with a scoped status check that respects the primary active project boundary.
16. If at least one non-logger scoped change exists, request one-time permission for a commit transaction. Do not request a persistent approval rule.
17. If only Session Logger memory/log files are dirty, explicitly ask whether to commit those logger-only changes.
18. Skip commit entirely only when there are no meaningful file changes, decisions, or progress to preserve.
19. Stage only active-project changes plus explicitly allowed repo-level files. Never stage another project.
20. Commit using `type(scope): specific outcome`.
21. For logger-only commits approved by the user, use a factual memory-outcome subject such as `docs(session-logger): record session decisions`.
22. Rerun scoped status.
23. Rewrite latest session logs only, not archive copies, so committed files move from `Remaining uncommitted` to `Committed` and only actual leftovers remain dirty.
24. Stage the rewritten latest logs and amend the same commit.
25. Confirm restart state is ready.
26. If continuing in the same thread, suggest `/compact`.

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
- Unclosed automatic plan markers, if any

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
- Auto Recovery Merge Notes, only if automatic entries affected the closeout

Omit sections that have no content.

---

## Auto Recovery Merge
`auto_recovery.md` is an active append-only buffer, not an archive.

At session end:

1. Read all entries.
2. Match plan-boundary BEFORE and AFTER entries by plan/checkpoint ID.
3. Preserve durable decisions, risks, relevant paths, verification, and BEFORE -> NOW deltas.
4. Surface any unmatched BEFORE entry as an unclosed plan marker.
5. Dedupe against existing hot/warm memory and current closeout content.
6. Clear `auto_recovery.md` only after the new latest summary and detailed logs have been written successfully.

If the merge fails, leave `auto_recovery.md` unchanged and report the failure.

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
3. If at least one non-logger scoped change exists, continue with the outcome commit flow.
4. If only Session Logger memory/log files are dirty, ask whether to commit those logger-only changes.
5. Skip commit entirely only when there are no meaningful file changes, decisions, or progress to preserve.
6. Stage only active-project changes plus explicitly allowed repo-level files.
7. Commit using the commit message rules below.
8. Rerun scoped status.
9. Rewrite only `last_session_summary.md` and `last_session_detailed.md` to reconcile `Committed`, `Remaining uncommitted`, `Skipped`, and `Failed`.
10. Stage the rewritten latest logs.
11. Amend the same commit.
12. Do not push unless the user explicitly requests it.

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
- Subject describes the actual work outcome, not logger mechanics.
- Avoid filler: `capture session end`, `update session log`, `misc changes`, and `session cleanup`.
- Logger-only commits are allowed only after explicit user choice and must use a factual memory-outcome subject, for example `docs(session-logger): record session decisions`.
- For large sessions, keep the subject short and put grouped details in the body.

---

## Completion Check
1. Can a fresh chat resume correctly from this project's `last_session_summary.md` alone? If not, improve it.
2. `last_session_detailed.md` has enough fidelity for warm recovery.
3. Useful `auto_recovery.md` entries were incorporated before the file was cleared.
4. If an automatic BEFORE entry lacks an AFTER entry, the latest logs surface it as unclosed.
5. Previous files were archived before being replaced.
6. Archive filenames are OS-safe and date-prefixed.
7. If a commit succeeded, latest logs reflect only remaining uncommitted state after the commit/amend.

---

## Must-Not-Do
- Do not end a session before explicit project activation.
- Do not write outside the primary active project's workflow directory.
- Do not append to a giant history log.
- Do not use unsafe filenames.
- Do not write empty placeholder sections.
- Do not overwrite latest-session files before archiving.
- Do not clear `auto_recovery.md` until useful entries are merged into latest session memory.
- Do not use `/compact` before writing the final session memory.
- Do not use `/fork` to bypass project access rules.
- Do not stage or commit another project without explicit authorization.
- Do not push to remote unless the user explicitly requests it.

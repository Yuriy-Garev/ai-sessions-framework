# Checkpoint Procedure

This file is scaffold material. When copied into a project, it operates only on that project's local `docs/workflow/` directory.

This procedure assumes the project has already been bootstrapped with `init project <project-name>` and then activated as primary.

## Purpose
Mid-session state capture. Manual `mid` produces a curated hot-summary checkpoint. Automatic safety capture preserves fast-moving state in append-only `auto_recovery.md` without clobbering hot memory.

No archiving. No rotation. Cheap.

---

## When to Use
- User explicitly evokes Session Logger and calls `mid` for the primary active project.
- An automatic safety entry is required before or after accepted implementation plan execution.
- A narrow non-plan safety event occurs: context pressure, before `/compact`, before `/fork`, or a major decision/milestone.

---

## Required Actions
1. Confirm the user explicitly activated this project as primary.
2. If the workflow scaffold is missing, stop and instruct the user to run `init project <project-name>`.
3. Use `/status` first when context pressure, worktree scope, or unfinished work is unclear.
4. For automatic safety capture, confirm current-thread hot recovery. If it has not happened, read only `project_identity.md` and `last_session_summary.md` first.
5. For automatic plan-boundary capture, append the BEFORE entry to `auto_recovery.md` before the first mutating execution step and append the AFTER entry after execution/verification before final response, `/compact`, or `/fork`.
6. For automatic non-plan safety capture, append a concise entry to `auto_recovery.md` and briefly tell the user what was captured and why.
7. For manual `mid`, read `project_identity.md`, `last_session_summary.md`, current manual context, and relevant `auto_recovery.md` entries.
8. For manual `mid`, overwrite this project's `docs/workflow/last_session_summary.md` with a curated checkpoint that blends durable facts and removes duplicates.
9. Mark the hot-summary file header with `[CHECKPOINT - session not closed]`.
10. Do not archive the previous summary.
11. Do not touch `last_session_detailed.md`.
12. Confirm to the user: checkpoint saved, session still open.
13. If continuing with a large context, suggest `/compact`.

---

## Manual Hot Summary Must Contain
- Title with `[CHECKPOINT]` prefix
- TL;DR: what has been done so far this session
- Current State: Done / In Progress / Blocked / Not Started
- Next Recommended Step
- Decisions This Session so far
- Active risks or watchouts
- Important references and git/worktree state when relevant

---

## Automatic Entry Must Contain
- Timestamp
- Source/trigger
- Plan/checkpoint ID when applicable
- Concise summary
- Important decisions
- Current risks/blockers
- Relevant paths/files
- Status: `open`, `resolved`, or `unclosed`

Automatic plan-boundary BEFORE entries must also contain prior state, intended plan, decisions/assumptions, expected files or scope, and risks.

Automatic plan-boundary AFTER entries must use the same plan/checkpoint ID and must also contain original intent summary, actual changes, decisions made during execution, verification, remaining work, and the BEFORE -> NOW delta.

If a BEFORE entry has no matching AFTER entry, later `start`, manual `mid`, or `end` must surface it as an unclosed plan marker.

---

## Auto Recovery File
`docs/workflow/auto_recovery.md` is active hot-adjacent workflow memory.

- Append automatic safety entries only.
- Do not overwrite existing entries during an active session.
- Do not treat it as archive history.
- Do not treat it as permission to read warm/cold/freezing memory.
- Manual `mid` may read relevant entries to curate hot summary.
- Session `end` must merge useful entries into normal session memory and clear the file only after successful incorporation.

---

## Must-Not-Do
- Do not checkpoint before explicit primary project activation.
- Do not write outside the primary active project's workflow directory.
- Do not write automatic safety entries to allowed read-only projects.
- Do not overwrite `last_session_summary.md` from automatic safety capture.
- Do not clear `auto_recovery.md` before session-end merge succeeds.
- Do not archive previous `last_session_summary.md`.
- Do not write `last_session_detailed.md`.
- Do not treat checkpoint as a session close.
- Do not use `/compact` before writing needed safety capture.
- Do not use `/fork` to bypass project access rules.
- Do not burn extra tokens on a full detailed write.

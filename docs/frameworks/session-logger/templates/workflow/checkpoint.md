# Checkpoint Procedure

This file is scaffold material. When copied into a project, it operates only on that project's local `docs/workflow/` directory.

This procedure assumes the project has already been bootstrapped with `init project <project-name>` and then activated as primary.

## Purpose
Mid-session state snapshot. Protects against session loss from crashes or context-limit hits.
No archiving. No rotation. Cheap.

---

## When to Use
- User explicitly evokes Session Logger and calls `mid` for the primary active project, or
- The agent signals context pressure, or
- A major decision or milestone was just reached mid-session.

---

## Required Actions
1. Confirm the user explicitly activated this project.
2. If the workflow scaffold is missing, stop and instruct the user to run `init project <project-name>`.
3. Use `/status` first when context pressure, worktree scope, or unfinished work is unclear.
4. Overwrite this project's `docs/workflow/last_session_summary.md` with current state.
5. Mark the file header with `[CHECKPOINT - session not closed]`.
6. Do not archive the previous summary.
7. Do not touch `last_session_detailed.md`.
8. Confirm to the user: checkpoint saved, session still open.
9. If continuing with a large context, suggest `/compact`.

---

## Checkpoint Summary Must Contain
- Title with `[CHECKPOINT]` prefix
- TL;DR: what has been done so far this session
- Current State: Done / In Progress / Blocked / Not Started
- Next Recommended Step
- Decisions This Session so far
- Any active risks

---

## Must-Not-Do
- Do not checkpoint before explicit project activation.
- Do not write outside the primary active project's workflow directory.
- Do not archive previous `last_session_summary.md`.
- Do not write `last_session_detailed.md`.
- Do not treat checkpoint as a session close.
- Do not use `/compact` before writing the checkpoint.
- Do not use `/fork` to bypass project access rules.
- Do not burn extra tokens on a full detailed write.

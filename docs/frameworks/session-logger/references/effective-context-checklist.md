# Effective Context Checklist

Use this checklist as a short manual preflight before Session Logger work begins.

This is an operator review surface, not a deeper context inventory.

## Project Scope

- Active project named?
- Project access allowed?
- Primary project is the only write target?

## Command Interpretation

- Skill explicitly invoked, or is this a permitted automatic safety entry?
- Interpreted command or trigger is clear?

## Memory Permissions

- Session memory level allowed?
- Warm/cold/freezing access explicitly granted if needed?
- `auto_recovery.md` use stays limited to automatic recovery entries and does not imply deeper memory access?

## Context Inclusion

- Any context included outside the primary project or explicitly allowed read-only projects? Must be no.
- Any write target outside the primary project? Must be no.

## Exclusions And Omissions

- What was omitted and why?
- Are read-only projects omitted unless explicitly relevant?

## Session Logger Safety

- If the project is uninitialized, is `init project <project-name>` being used instead of `activate` or `start`?
- If automatic safety capture is running, has current-thread hot recovery happened?
- If session end is running, will `auto_recovery.md` be cleared only after successful merge?

## Outcome

- `Go` when project scope, interpreted command/trigger, and allowed memory level are all valid.
- `Stop` when project scope, memory scope, or automatic write target is invalid.
- `Ask` when the user must resolve an ambiguity, such as warm permission or logger-only commit choice.

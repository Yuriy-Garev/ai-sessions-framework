# Session Logger Activation Audit

This shared reference defines the compact runtime-facing audit block that Session Logger should be able to show when invoked.

The block explains the current run in the moment. It does not replace the deeper context inventory.

## Purpose

Use the audit block to explain:

- why Session Logger activated
- what command or automatic safety trigger it interpreted
- what project is primary
- what short project refs resolved the request
- what projects are allowed read-only
- whether the project index was used
- what memory level is allowed
- what rules constrained the run
- what sources were allowed or omitted

## Canonical Shape

```markdown
## Session Logger Audit

- Activation reason:
- Interpreted command or trigger:
- Primary active project:
- Primary project short ID:
- Allowed read-only projects:
- Allowed read-only short IDs:
- Project index used:
- Initialization state:
- Allowed memory level:
- Skill invocation or automatic exception evidence:
- Constraint rules:
- Allowed sources:
- Omitted sources:
- Next valid actions:
```

## Field Semantics

- `Activation reason` explains why Session Logger is running at all. It should include the positive trigger and the non-trigger boundary when useful.
- `Interpreted command or trigger` is the parsed Session Logger action or automatic safety trigger.
- `Primary active project` names the read/write Session Logger project for this run.
- `Allowed read-only projects` names any extra activated projects that may be inspected but not written.
- `Project index used` states whether the local `.agents/projects-index.json` was read for selection or listing.
- `Initialization state` states whether the project scaffold exists.
- `Allowed memory level` states the currently permitted recovery level for this run.
- `Skill invocation or automatic exception evidence` identifies explicit invocation, or the automatic safety exception for plan-boundary/context-pressure capture.
- `Constraint rules` names the rules that materially constrained the run, such as explicit invocation gate, primary-project write scope, read-only allowed projects, warm permission requirement, init-first requirement, and no unindexed project memory.
- `Allowed sources` summarizes what sources may be used for this run.
- `Omitted sources` summarizes what was not used and why.
- `Next valid actions` lists the next actions permitted by the current state and permissions.

For `help`, the allowed source is only `.agents/skills/session-logger/HELP.md`; project index, project memory, and framework docs are omitted.

For automatic safety capture, allowed write target is only `docs/workflow/auto_recovery.md` in the primary active project. Automatic capture does not authorize warm/cold/freezing reads.

## Timing

Show this block live when Session Logger is invoked or about to act. For automatic non-plan safety entries, a brief notice is enough unless the user asks for the full audit block.

## Relationship To The Context Inventory

The activation audit is the live explanation surface.

The context inventory remains the deeper audit surface for describing included sources, permissions, freshness, and exclusions in more detail.

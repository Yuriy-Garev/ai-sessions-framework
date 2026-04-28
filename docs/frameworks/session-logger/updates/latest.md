# Session Logger Framework Latest

framework: session-logger
latest_framework_update_date: 2026-04-28

## Summary

Current framework head for Session Logger project-scoped memory, including append-only automatic safety capture.

## Current Methodology Highlights

- explicit lifecycle: `index projects -> init project|activate -> audit framework -> start -> mid/end`
- project-local workflow memory only
- manual-only framework audit
- project-local framework audit state in `.codex/session-logger.toml`
- explicit approval for specific upgrade actions
- repo-level project index for safe project selection
- short project refs backed by full UUID project IDs
- registry access metadata with external read-only write blocking
- primary read/write project plus allowed read-only project activation
- add-read-only activation with `activate + <project-ref>`
- automatic safety entries before and after accepted implementation plan execution
- append-only `docs/workflow/auto_recovery.md` for automatic safety capture
- manual `mid` blends hot summary with relevant automatic recovery entries
- session `end` merges and clears auto recovery only after successful incorporation

## Current Governance Boundaries

Session Logger governs only:

- `.codex/session-logger.toml`
- `docs/workflow/`
- project-local Session Logger procedure and scaffold files

Automatic safety entries govern only append writes to `docs/workflow/auto_recovery.md` in the active primary project. They do not broaden manual framework audit scope, grant deeper memory access, or grant access to unrelated project code.

Projects marked `access_mode: "external_read_only"` or `write_policy: "forbidden"` in `.agents/projects-index.json` are reference-only. They may be listed, resolved, and activated only as allowed read-only projects; they must never receive Session Logger writes or become primary.

It does not govern unrelated project code.

## Use

Manual audit compares the primary active project's adopted framework date against this date and then reads newer dated update manifests if needed.

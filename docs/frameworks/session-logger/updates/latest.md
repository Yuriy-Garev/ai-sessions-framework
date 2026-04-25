# Session Logger Framework Latest

framework: session-logger
latest_framework_update_date: 2026-04-25

## Summary

Current framework head for the manual Session Logger audit flow.

## Current Methodology Highlights

- explicit lifecycle: `index projects -> init project|activate -> audit framework -> start -> mid/end`
- project-local workflow memory only
- manual-only framework audit
- project-local framework audit state in `.codex/session-logger.toml`
- explicit approval for specific upgrade actions
- repo-level project index for safe project selection
- short project refs backed by full UUID project IDs
- primary read/write project plus allowed read-only project activation
- add-read-only activation with `activate + <project-ref>`

## Current Governance Boundaries

Session Logger governs only:

- `.codex/session-logger.toml`
- `docs/workflow/`
- project-local Session Logger procedure and scaffold files

It does not govern unrelated project code.

## Use

Manual audit compares the primary active project's adopted framework date against this date and then reads newer dated update manifests if needed.

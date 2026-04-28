# Auto Recovery

Append-only active-session recovery buffer for automatic Session Logger safety entries.

This file is hot-adjacent workflow memory. It is not archive history, not warm/cold/freezing memory, and not a replacement for curated hot summary.

Manual `mid` may read relevant entries and blend durable facts into `last_session_summary.md`.
Session `end` must merge useful entries into normal session memory and clear this file only after successful incorporation.

---

## Entry Template

```markdown
### [timestamp] [source/trigger]
- Plan ID: [plan/checkpoint-id or n/a]
- Status: open|resolved|unclosed
- Summary:
- Important decisions:
- Risks/blockers:
- Relevant paths/files:
```

For plan-boundary BEFORE entries, include prior state, intended plan, assumptions, expected scope, and risks.

For plan-boundary AFTER entries, use the same plan ID and include original intent summary, actual outcome, changed decisions, verification, remaining work, and BEFORE -> NOW delta.

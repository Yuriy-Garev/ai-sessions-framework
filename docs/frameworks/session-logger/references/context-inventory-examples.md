# Session Logger Context Inventory Examples

These examples show how to describe Session Logger operations using the shared v1 context inventory format.

## Help

```yaml
context_operation: help
primary_active_project: null
primary_active_project_short_id: null
allowed_read_only_projects: []
allowed_read_only_project_short_ids: []
project_index_used: false
topic_status: not_applicable
items:
  - source: .agents/skills/session-logger/HELP.md
    scope: shared_repo_reference
    permission_level: reference
    freshness: reference
    inclusion_reason: explicit help command prints the maintained command overview
    used_for: user-facing command help
```

Excluded on purpose: project index, project workflow memory, project code, and framework docs.

## Hot-Only Start

```yaml
context_operation: start
primary_active_project: <project-name>
primary_active_project_short_id: <short-id>
allowed_read_only_projects: []
allowed_read_only_project_short_ids: []
project_index_used: false
topic_status: pending
items:
  - source: projects/<project-name>/docs/workflow/project_identity.md
    scope: primary_active_project
    permission_level: hot
    freshness: static
    inclusion_reason: stable project facts shape the recovery brief
    used_for: identity
  - source: projects/<project-name>/docs/workflow/last_session_summary.md
    scope: primary_active_project
    permission_level: hot
    freshness: current
    inclusion_reason: hot memory is the default restart surface
    used_for: topic recovery
  - source: projects/<project-name>/docs/workflow/auto_recovery.md
    scope: primary_active_project
    permission_level: auto_recovery
    freshness: current
    inclusion_reason: unresolved automatic entries may affect restart accuracy
    used_for: unclosed-plan and safety marker review
```

Excluded on purpose: warm memory, archives, framework docs as live memory, and any unactivated project.

## Manual Mid

```yaml
context_operation: mid
primary_active_project: <project-name>
primary_active_project_short_id: <short-id>
allowed_read_only_projects: []
allowed_read_only_project_short_ids: []
project_index_used: false
topic_status: known
items:
  - source: projects/<project-name>/docs/workflow/project_identity.md
    scope: primary_active_project
    permission_level: hot
    freshness: static
    inclusion_reason: checkpoint summary should stay aligned with project identity and constraints
    used_for: checkpoint framing
  - source: projects/<project-name>/docs/workflow/last_session_summary.md
    scope: primary_active_project
    permission_level: hot
    freshness: current
    inclusion_reason: existing hot summary is being curated into a newer checkpoint
    used_for: checkpoint continuity
  - source: projects/<project-name>/docs/workflow/auto_recovery.md
    scope: primary_active_project
    permission_level: auto_recovery
    freshness: current
    inclusion_reason: automatic entries may contain decisions or plan deltas to merge
    used_for: deduped checkpoint blending
```

Excluded on purpose: `last_session_detailed.md`, archives, and allowed read-only project writes.

## Automatic Safety Entry

```yaml
context_operation: automatic safety entry
primary_active_project: <project-name>
primary_active_project_short_id: <short-id>
allowed_read_only_projects: []
allowed_read_only_project_short_ids: []
project_index_used: false
topic_status: known
items:
  - source: projects/<project-name>/docs/workflow/project_identity.md
    scope: primary_active_project
    permission_level: hot
    freshness: static
    inclusion_reason: hot recovery prerequisite before automatic capture
    used_for: scope confirmation
  - source: projects/<project-name>/docs/workflow/last_session_summary.md
    scope: primary_active_project
    permission_level: hot
    freshness: current
    inclusion_reason: hot recovery prerequisite before automatic capture
    used_for: prior-state framing
  - source: projects/<project-name>/docs/workflow/auto_recovery.md
    scope: primary_active_project
    permission_level: auto_recovery
    freshness: current
    inclusion_reason: append-only target for automatic safety capture
    used_for: durable non-destructive recovery entry
```

Excluded on purpose: hot summary writes, warm memory, archive reads, and all read-only project writes.

## End With Auto Recovery Merge

```yaml
context_operation: end
primary_active_project: <project-name>
primary_active_project_short_id: <short-id>
allowed_read_only_projects: []
allowed_read_only_project_short_ids: []
project_index_used: false
topic_status: known
items:
  - source: projects/<project-name>/docs/workflow/last_session_summary.md
    scope: primary_active_project
    permission_level: hot
    freshness: current
    inclusion_reason: latest summary is archived and replaced during session end
    used_for: closeout continuity
  - source: projects/<project-name>/docs/workflow/auto_recovery.md
    scope: primary_active_project
    permission_level: auto_recovery
    freshness: current
    inclusion_reason: automatic entries must be merged before cleanup
    used_for: closeout merge and unclosed-plan detection
```

Excluded on purpose: archives as recovery input, unactivated projects, and remote pushes.

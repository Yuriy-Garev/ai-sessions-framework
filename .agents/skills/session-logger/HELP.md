# Session Logger Help

Use Session Logger by explicitly invoking it, for example:

```text
$session-logger help
```

`help all` prints this same overview.

## Project Setup

```text
$session-logger index projects
$session-logger list projects
$session-logger init project my-project
```

- `index projects` updates the project registry from immediate `projects/` folders and scaffold/config existence only.
- `list projects` shows short IDs, full IDs, names, paths, scaffold status, and last activation dates.
- `init project my-project` normalizes the name, creates the workflow scaffold, registers the project, and activates it as primary.

## Activation

```text
$session-logger activate context-framework
$session-logger activate 48b98e5c
$session-logger activate app + docs-site, api-service
$session-logger activate + docs-site
$session-logger deactivate app
$session-logger deactivate all
```

- The primary project is read/write for Session Logger workflow memory.
- Projects after `+` are allowed read-only projects.
- `activate + docs-site` adds a read-only project without changing the current primary.
- Project refs may be names, aliases, short IDs, or full UUIDs.
- `deactivate` is canonical. `disactivate` is tolerated as an alias.

## Session Flow

```text
$session-logger start
$session-logger mid
$session-logger end
```

- `start` recovers from hot memory after activation.
- If no project is active, `start` can suggest recent projects before reading project memory.
- `mid` writes a cheap checkpoint to the primary project only.
- `end` closes the session, writes logs, and may attempt scoped git reconciliation.

## Audit

```text
$session-logger audit framework
```

- Runs the manual framework audit for the primary active project only.
- Proposed upgrade actions must be specific and approval-gated.

## Safety Notes

- `help` prints this file only; it does not read project memory or the project index.
- `index projects` never reads project code or workflow memory contents.
- Warm, cold, and freezing memory still require explicit permission.
- Allowed read-only projects must not receive checkpoints, session logs, framework upgrades, commits, or memory writes.

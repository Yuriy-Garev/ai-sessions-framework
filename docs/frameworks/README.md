# Frameworks Directory

This folder is for reusable process frameworks that are broader than a single skill.

Frameworks here are shared documentation, reference material, templates, and scaffolding. They are not live project state and must not be used as project memory.

## Recommended convention

```text
docs/frameworks/<framework-name>/
  README.md
  references/
  templates/
```

## How to add a framework

1. Create a new subfolder.
2. Describe:
   - purpose
   - scope
   - trigger conditions
   - operating procedure
   - files it owns or expects
   - non-goals
3. If Codex should use the framework automatically:
   - mention it from `AGENTS.md`, or
   - create a dedicated active skill in `.agents/skills/`

## Current frameworks

- `session-logger/` - reusable session-memory, restart, checkpoint, and archive workflow docs/templates.
- `_framework-template/` - copy this to scaffold another framework.

Live session memory belongs inside the explicitly activated project, not in this directory.

# Repo-Local Skills

This directory is for active reusable skills only.

Each active skill must live in its own subfolder:

```text
.agents/skills/<skill-name>/
  SKILL.md
```

Do not place templates, examples, or inactive scaffolding in this directory if they include a `SKILL.md` file. Put reusable skill scaffolding under `.agents/templates/` and use non-discoverable filenames such as `SKILL.template.md`.

Optional additions for active skills:

- `scripts/` - executable helpers
- `references/` - docs Codex can read when the skill activates
- `assets/` - templates or static resources
- `agents/openai.yaml` - optional metadata

Keep each skill narrow and explicit. The `description` field should say when the skill should and should not trigger.

---
name: cli-docs-creator
description: Create, review, and maintain CLI documentation for terminal help systems. Use when Codex needs to design or improve CLI --help output, command trees, usage text, examples, parser help, error recovery help, snapshot tests for help output, terminal docs, or root/module/leaf command navigation.
---

# CLI Docs Creator

Use this skill to make CLI help behave like a navigation system, not a parser dump. A user should be able to start at root help, descend through module help, open a leaf command help page, and copy an example that succeeds.

## Workflow

1. Map the command tree before editing help.
   - Identify root commands, modules, leaf commands, shared options, inherited options, and aliases.
   - If the command tree is already implemented, inspect real help output instead of guessing.
2. Classify each help page.
   - Root help explains the product, command map, common workflows, global options, and how to get more detail.
   - Module help acts as a directory for a task area and routes users to the right leaf command.
   - Leaf help is directly runnable: it explains inputs, options, defaults, examples, output, side effects, and related commands.
3. Improve the highest-friction pages first.
   - Fix stale syntax and parser mismatches.
   - Add one working example early on each page.
   - Make required inputs obvious.
   - Surface defaults, environment variables, and side effects where they affect user choices.
   - Separate local options from inherited or global options.
   - Add next-step hints such as `Use '<cli> <command> --help' for details.`
4. Treat errors as documentation moments.
   - Missing required input should produce concise corrective help, not only a parser wall.
   - Send help to stdout and diagnostics to stderr.
   - Ensure `-h` and `--help` exit successfully.
5. Validate help like an interface contract.
   - Run real help commands where possible.
   - Add or update tests for help output and exit behavior when the codebase supports them.
   - Verify examples are executable or close enough to be honestly copy-pasteable.

## Reference Loading

Load only the reference needed for the current task:

- `references/help-blueprint.md` for root, module, and leaf help templates; section order; and copy-paste example patterns.
- `references/audit-checklist.md` for review checklists, common failure modes, anti-patterns, and maintenance gates.
- `references/ecosystem-notes.md` for brief implementation notes for Commander, Cobra, Click, clap, argparse, and docopt.
- `references/source-basis.md` when the user asks where the standards come from or wants external reference links.

## Default Standard

Optimize for this path:

```bash
<cli> --help
<cli> <module> --help
<cli> <module> <command> --help
```

The help system is good enough when a new user can identify the right command, understand required inputs, and run a common example without source-code inspection or outside explanation.

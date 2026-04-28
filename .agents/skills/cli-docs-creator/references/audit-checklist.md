# CLI Help Audit Checklist

Use this reference when reviewing existing CLI help or defining acceptance checks.

## Universal Checks

- First line explains what the command or module is.
- Usage line matches the real parser.
- Required inputs are visually obvious.
- Examples are present and realistic.
- `-h` and `--help` are supported and documented where appropriate.
- Wording is concise and concrete.
- No dead flags, stale command names, or contradictory defaults.
- Output remains readable in a plain terminal without color.
- Help prints to stdout.
- Errors and diagnostics print to stderr.
- Help exits with code 0.
- Invalid invocations exit non-zero.

## Root Page Checks

- Common commands are surfaced early.
- Commands are grouped by task when the set is large.
- There is a clear navigation hint for deeper help.
- Global options are present but do not dominate the page.

## Module Page Checks

- Module purpose is clear.
- Subcommands are discoverable.
- Shared module flags are documented.
- Parent or global flags are separated.
- The page routes users to the right leaf help.

## Leaf Page Checks

- One example solves the common case directly.
- Options have plain-language descriptions.
- Defaults are shown where helpful.
- Environment variables are shown where relevant.
- Side effects and destructive behavior are called out.
- Related commands are linked or named.

## Common Failure Modes

1. Parser dump: technically complete but human-useless.
2. Missing hierarchy: root does not guide users to modules or leaves.
3. No examples: syntax exists, but successful use is unclear.
4. Hidden defaults: users must guess runtime behavior.
5. Option soup: too many flags with no grouping or prioritization.
6. Stale help: output disagrees with implementation.
7. Inconsistent vocabulary: one concept has multiple names.
8. No recovery path: errors do not guide the user.

## Improvement Order

1. Fix wrong or stale content.
2. Add one-line purpose statements.
3. Add or improve examples.
4. Reorder for scanability.
5. Split local options from inherited options.
6. Add see-also or next-step hints.
7. Normalize terminology and metavariables.
8. Improve formatting and wrapping.

## Error Help

A failed invocation should say:

- what was wrong;
- what the user likely needs to provide or change;
- the corrected usage line;
- one minimal example;
- the help command to run next.

Example:

```text
error: missing required argument <service>

Usage:
  chain logs tail <service> [options]

Example:
  chain logs tail api

Run 'chain logs tail --help' for more information.
```

## Anti-Patterns

- Raw parser output with no task framing.
- No examples.
- Alphabetical page structure before importance.
- Parent flags mixed into leaf options without labeling.
- Redefining `-h` for anything other than help.
- Placeholder examples that cannot work.
- Web docs required for basic usage.
- Color or formatting that breaks when piped.
- Hidden or deprecated commands shown as normal public surface.

## Maintenance Gates

Review help whenever any of these change:

- command names or aliases;
- flags;
- defaults;
- environment variables;
- examples;
- output modes;
- destructive behavior;
- command side effects.

Prefer snapshot tests for critical help pages and exit-behavior tests for `--help`, `--version`, invalid invocations, stdout, and stderr.

## Acceptance Path

The help system passes when a new user can:

1. run `<cli> --help`;
2. identify the correct module;
3. run `<cli> <module> --help`;
4. identify the correct leaf command;
5. run `<cli> <module> <command> --help`;
6. copy one example and succeed.

# CLI Help Ecosystem Notes

Use this reference for framework-specific help implementation hints. Keep the general help standard from `SKILL.md` and `help-blueprint.md` as the source of truth.

## Commander

- Commander provides `--help`, `-h`, and `help` behavior for command trees.
- Use command descriptions, option descriptions, examples in `addHelpText`, and explicit aliases where useful.
- Check that custom error handlers keep help on stdout and diagnostics on stderr.
- Snapshot root, module, and leaf help because Commander output can change when commands or options move.

## Cobra

- Cobra supports help subcommands and `--help` automatically.
- Use command grouping and custom help templates for larger CLIs.
- Keep `Short` text concise for command lists and `Long` text useful on command pages.
- Add examples in the `Example` field and keep them runnable.

## Click

- Click supports short help, epilog text, defaults, environment variables, and wrapping.
- Consider explicit `-h` support if the project wants both `-h` and `--help`.
- Click arguments often need explanation in command help text because argument help is not as direct as option help.

## clap

- clap has strong support for headings, subcommand headings, before/after help blocks, next-line help, and color control.
- Use custom headings to separate local, inherited, and global options.
- Prefer explicit value names and possible values where they improve scanability.

## argparse

- argparse supports subparser grouping, parent parsers, default display formatters, raw description formatters, and metavars.
- Use `RawDescriptionHelpFormatter` or similar when examples need stable formatting.
- Add concise custom error handling for missing required inputs when parser walls are too noisy.

## docopt

- docopt is useful when help text is the interface grammar.
- Treat the usage block as a contract and test examples against it.
- Keep usage alternatives readable; split overloaded commands into clearer subcommands when grammar grows too dense.

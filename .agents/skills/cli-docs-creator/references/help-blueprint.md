# CLI Help Blueprint

Use this reference when creating or rewriting CLI help pages.

## Core Model

Help is a command navigation system. Each page should answer:

1. Where am I?
2. What does this command or module do?
3. What is the syntax?
4. What are the most common next actions?
5. Which arguments and flags matter now?
6. What examples can I copy?
7. Where do I go next?

## Recommended Anatomy

Use this order unless a CLI framework strongly constrains output:

1. Name plus one-line description
2. Usage or synopsis
3. Examples
4. Commands for non-leaf pages
5. Arguments
6. Options
7. Parent or inherited options
8. Environment variables
9. Exit behavior or notes
10. See also, docs, or support

Put the highest-value information first. For humans this usually means purpose, one working example, then details.

## Root Help

Root help must:

- State the product purpose in one sentence.
- Show the command map.
- Highlight common workflows.
- Teach how to descend the tree.

Template:

```text
chain - Manage deployments, logs, and environments.

Usage:
  chain [global options] <command> [args]

Start here:
  chain auth login              Authenticate with the platform
  chain env list                List environments
  chain deploy create --help    Create a deployment
  chain logs tail --help        Stream logs from a service

Commands:
  auth       Sign in, sign out, and inspect identity
  env        Manage environments and configuration
  deploy     Create, inspect, and manage deployments
  logs       View and stream logs

Global options:
  -h, --help        Show help
      --version     Show version
      --profile     Select profile
      --output       Output format (table|json|yaml)
      --no-color     Disable color

Learn more:
  Use 'chain <command> --help' for details.
  Use 'chain help <command>' if you prefer help subcommands.
```

Avoid listing every obscure internal command at root. Keep global options visible but secondary.

## Module Help

A module page is a directory for one task area.

Module help must:

- Define the module responsibility.
- Show important subcommands.
- Explain module-specific syntax and shared flags.
- Route the user to the right leaf command.

Template:

```text
chain deploy - Create and manage deployments.

Usage:
  chain deploy <command> [options]

Examples:
  chain deploy create --service api --env prod
  chain deploy status --id dep_123
  chain deploy rollback --id dep_123

Commands:
  create      Create a new deployment
  status      Show deployment status
  list        List recent deployments
  rollback    Roll back a deployment
  cancel      Cancel a deployment in progress

Options:
      --env <name>       Environment to target
      --output <format>  table|json|yaml
  -h, --help             Show help

Parent options:
      --profile <name>   Profile to use
      --no-color         Disable color

See also:
  Use 'chain deploy <command> --help' for command-specific examples.
```

Group long command lists by intent, such as create/update/delete, inspect/debug/audit, local/remote, or safe/destructive.

## Leaf Help

A leaf page must be directly actionable. A user should be able to copy the common example, change one or two values, and run it.

Leaf help must:

- State exactly what the command does.
- Show exact syntax.
- Clarify required and optional inputs.
- Explain flags in plain language.
- Provide copy-paste examples.
- Explain output behavior, side effects, and related commands when relevant.

Template:

```text
chain logs tail - Stream logs from a service.

Usage:
  chain logs tail <service> [options]

Arguments:
  <service>              Service name or ID

Options:
      --env <name>       Environment to read from (default: current)
      --since <duration> Show logs newer than this duration (e.g. 15m, 2h)
      --json             Emit JSON lines
      --plain            Disable rich formatting
  -n, --lines <count>    Show this many lines before streaming (default: 100)
  -h, --help             Show help

Examples:
  chain logs tail api
  chain logs tail api --env prod --since 30m
  chain logs tail api --json | jq .message

See also:
  chain logs query --help
  chain logs export --help
```

Clarify required operands, defaults, accepted units, mutually exclusive flags, destructive side effects, stdin support, and `--` separator behavior when those details matter.

## Examples

Lead with the common successful path. Prefer realistic names and values.

Weak:

```text
foo --bar BAZ --qux QUUX
```

Better:

```text
chain deploy create --service api --env prod
```

Best:

```text
chain deploy create --service api --env prod --wait
```

Use output examples only when they clarify behavior.

## Wording

Use direct, concrete descriptions:

- `Create a deployment.`
- `Stream logs from a service.`
- `Wait for the deployment to complete.`
- `Write output as JSON.`
- `Read config from this file.`

Avoid filler such as `This command allows users to...`, `Used to...`, or vague labels such as `Operations` and `Management`.

# Tool Commands

The MCP builder tool registry — the same surface Claude Desktop and other MCP clients reach over stdio — is also reachable directly from the CLI as plain function calls. No subprocess, no protocol round-trip.

These are agent-facing surfaces; they live under `bb dev` to keep the top-level `bb --help` focused on the human-facing build/deploy/standards verbs.

| Command | Purpose |
|---|---|
| [`bb dev tools list`](#dev-tools-list) | List every fine-grained tool with its JSON schema |
| [`bb dev tools call <name>`](#dev-tools-call) | Invoke a single tool by name |
| [`bb session list \| show \| reset`](#session) | Inspect, dump, or reset persisted builder sessions |
| [`bb dev resources list \| read`](#dev-resources) | Browse the static resource registry (recipes, skills, error patterns, etc.) |

## dev tools list

Lists every builder tool with its full JSON schema.

```bash
bb dev tools list                              # full schemas, as JSON
bb dev tools list --names                      # just tool names, one per line
```

**Flags:**

| Flag | Description |
|---|---|
| `--names` | Print only the tool names |

## dev tools call

Invokes a single fine-grained builder tool by name. Args come from `--args` (inline JSON) or `--args-file` (path to a JSON file).

```bash
bb dev tools call get_current_timestamp
bb dev tools call get_skill_instructions --args '{"skillId":"smart-token"}'
bb dev tools call set_collection_metadata --args-file ./metadata.json --session demo
```

**Flags:**

| Flag | Description |
|---|---|
| `--args <json>` | Tool arguments as an inline JSON string |
| `--args-file <path>` | Tool arguments read from a JSON file |
| `--session <id>` | Session id for stateful tools. Persisted to `~/.bitbadges/sessions/<id>.json`. Defaults to `--args.sessionId`, or the built-in default session. |
| `--raw` | Print the structured result instead of the formatted text block |

Stateful tools (`set_*`, `add_*`, `remove_*`, `get_transaction`, …) read from and write to the named session. Sessions survive across invocations, so agents can compose a collection across many CLI calls:

```bash
SESSION=demo
bb dev tools call set_standards --session $SESSION --args '{"standards":["SmartToken"]}'
bb dev tools call set_collection_metadata --session $SESSION --args-file ./meta.json
bb dev tools call add_approval --session $SESSION --args-file ./approval.json
bb dev tools call get_transaction --session $SESSION  # emit final JSON
```

Unknown tool names exit `1` and print the available tool list on stderr.

## session

Persisted builder sessions live under `~/.bitbadges/sessions/<id>.json`. Use the `session` subcommand to inspect or reset them.

```bash
bb session list                       # session ids on disk
bb session show demo                  # snapshot as JSON
bb session reset demo                 # delete the file
```

| Subcommand | Purpose |
|---|---|
| `list` | Print every persisted session id, one per line |
| `show <id>` | Print the session snapshot as JSON |
| `reset <id>` | Delete the session file |

## dev resources

Static MCP resources — the bundled token registry, recipes, skills, error patterns, and docs slugs. Same surface the MCP clients see, reachable from the CLI for inspection or scripting.

```bash
bb dev resources list                     # full metadata as JSON
bb dev resources list --uris              # just URIs
bb dev resources read bitbadges://recipes/all
```

| Subcommand | Flag | Description |
|---|---|---|
| `list` | `--uris` | Print only the resource URIs |
| `read <uri>` | — | Read a resource body by URI (e.g. `bitbadges://recipes/all`) |

## See also

- [Build Commands](build-commands.md) — flag-based deterministic builders. Use these instead of composing tool calls when a template fits.
- [Analysis Commands](analysis-commands.md) — `check`, `explain`, `simulate`, `preview` for the assembled tx.
- The MCP builder server (`bitbadges-builder` bin) exposes the same registry over stdio — point any MCP client at it for the same surface in Claude Desktop, Cursor, etc.

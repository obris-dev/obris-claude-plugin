# Obris Claude Code Plugin

Connect your [Obris](https://obris.ai) knowledge base to Claude Code.

Provides:

- **MCP server** — list topics, read items, save knowledge, and capture feedback from any Claude Code session
- **`/obris:sync`** — sync a topic to a local directory (pulls remote items, pushes local edits, bulk-uploads via `--add-all`, exclude/include rules per directory)
- **`/obris:add`** — push a single local file into a synced topic
- **`/obris:link`** — relink a renamed file to its remote item
- **`/obris:auth`** — set up Obris CLI authentication

## Common workflows

- **Bootstrap a new directory into a topic.** Find or create the topic, then `obris sync -p <dir> -t <id> --add-all`. Subdirs become subtopics; files in `.env`, `.ssh/`, `.aws/`, `.git/` etc. are skipped by default.
- **Pull a topic down to inspect its files.** `obris sync -p <dir> -t <id>` with no `--add-all`. Pulls every item; doesn't upload local files.
- **Keep a working directory synced.** Just `obris sync -p <dir>` — no flags. It picks up local edits and remote changes, surfaces conflicts, and reports new untracked files without uploading them.
- **Override a default exclusion** (e.g., a `.env.example` you actually do want synced). `obris sync include .env.example -p <dir>`. Last-call-wins; the inverse is `obris sync exclude`.

## Install

In a running Claude Code session:

```
/plugin marketplace add obris-dev/obris-claude-plugin
/plugin install obris@obris
```

Then open the plugin manager and activate it:

```
/plugin
```

Reload to pick up the skills and MCP server:

```
/reload-plugins
```

## Prerequisites

The sync, add, and link skills shell out to the `obris` CLI. Install it once:

```bash
pip install obris-cli
obris auth login
```

The PyPI package is `obris-cli`; the installed command is `obris`.

`obris auth login` opens a browser for authentication. No API keys needed. When running inside an AI assistant (which can't block on a polling loop), use the two-step flow instead: `obris auth login --no-wait` to print the URL, then `obris auth complete` after the user authorizes.

The MCP server works without the CLI installed; the CLI is only needed for disk-level sync commands.

## Troubleshooting

### 403 when the plugin calls the CLI

Claude Code sandboxes network access. If calling `obris auth login` (or any other `obris` command) from inside the plugin returns a 403, add the Obris domains to the allowlist:

1. Open Claude Code **Settings → Capabilities → Domain allowlist**
2. Add:
   - `*.obris.ai`
   - `obris-uploads.s3.amazonaws.com`
3. Retry the command

## Links

- [Obris](https://obris.ai)
- [Obris CLI on PyPI](https://pypi.org/project/obris-cli/)
- [Documentation](https://docs.obris.ai)

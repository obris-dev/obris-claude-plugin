---
name: sync
description: Sync an Obris topic to a local directory. Pulls remote items down, pushes local edits back. Use when you need topic files available on disk.
---

# Sync Obris Topic

## Prerequisites

Requires the `obris` CLI. If not installed, tell the user to run:

```bash
pip install obris-cli
```

The PyPI package is `obris-cli`; the installed command is `obris`. Do
not `pip install obris` — that's a different, older package.

Authentication uses a two-step browser flow. See the `/obris:auth`
skill for details — the short version is `obris auth login --no-wait`,
show the user the printed URL, then `obris auth complete` once they
signal they've authorized.

## Instructions

Sync an Obris topic to a local directory using the `obris` CLI.

### First sync (link a topic to a directory)

Use the Obris MCP tools to find the topic first:
1. Call `list_topics` to find the topic the user is referring to
2. Run the sync command with the topic ID:

```bash
obris sync -p <directory> -t <topic-id>
```

### Subsequent syncs

If the directory is already linked to a topic:

```bash
obris sync -p <directory>
```

### Selective sync

To sync specific items only, use `--item`:

```bash
obris sync -p <directory> -i <item-id> -i <item-id>
```

Use the MCP `get_topic_knowledge` tool to list items and their IDs if the user wants to pick specific ones.

### Dry run

Always offer a dry run first if the user seems unsure:

```bash
obris sync -p <directory> --dry-run
```

### Flags

- `-p, --path` — Local directory (defaults to current directory)
- `-t, --topic` — Topic ID (required for first sync)
- `-i, --item` — Specific item IDs (repeatable)
- `--dry-run` — Preview without making changes
- `-y, --yes` — Auto-confirm prompts

### After syncing

Tell the user what was pulled/pushed and where the files are. If there were conflicts, explain the conflict files and how to resolve them.

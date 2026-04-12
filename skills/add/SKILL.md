---
name: add
description: Add a local file to a synced Obris topic. Use when you want to push a file from disk to your knowledge base.
---

# Add File to Obris Topic

## Prerequisites

Requires the `obris` CLI (`pip install obris-cli` — note the package
name is `obris-cli`, not `obris`) and authentication via the
`/obris:auth` skill (two-step `obris auth login --no-wait` + `obris auth
complete` flow).

## Instructions

Add a local file to a synced topic using the `obris` CLI.

```bash
obris sync add <filepath> -t <topic-id>
```

If the directory is synced to exactly one topic, the `-t` flag is optional — it auto-detects.

### Steps

1. Resolve the file path from the user's request
2. If the user hasn't specified a topic, check if the file's directory is already synced to a topic
3. If multiple topics sync to that directory, use the MCP `list_topics` tool to show them by name and ask the user which one
4. Run the add command
5. Confirm what was added and to which topic

### Notes

- The file must exist on disk
- If the file is already tracked by the topic, the CLI will tell you — use `obris sync` to push edits instead
- After adding, the file is tracked and will sync on future `obris sync` runs

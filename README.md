# Obris Claude Plugin

Connect your [Obris](https://obris.ai) knowledge base to Claude. Works in
Claude Code (the terminal CLI) and in Cowork (the desktop and web app).

The plugin's goal is to sync your Obris knowledge to disk through the
`obris` CLI, where Claude can read your topics as files. That's faster
than calling a server every time, and it leaves Claude more room to
focus on what you actually asked. The bundled MCP server keeps your
knowledge reachable when Claude doesn't have filesystem access.

Provides:

- **MCP server**: List topics, read items, and save knowledge from any
  Claude Code or Cowork session.
- **`/obris:sync`**: Sync a topic to a local directory (pulls remote
  items, pushes local edits, bulk-uploads via `--add-all`, unlinks
  tracked files, manages exclude/include rules per directory).
- **`/obris:add`**: Push a single local file into a synced topic.
- **`/obris:link`**: Relink a renamed file to its remote item.
- **`/obris:auth`**: Set up Obris CLI authentication.

## Common workflows

- **Bootstrap a new directory into a topic.** Find or create the topic, then
  `obris sync -p <dir> -t <id> --add-all`. Subdirs become subtopics; files
  in `.env`, `.ssh/`, `.aws/`, `.git/` etc. are skipped by default.
- **Pull a topic down to inspect its files.** `obris sync -p <dir> -t <id>`
  with no `--add-all`. Pulls every item; doesn't upload local files.
- **Keep a working directory synced.** Just `obris sync -p <dir>` with no
  flags. It picks up local edits and remote changes, surfaces conflicts,
  and reports new untracked files without uploading them.
- **Override a default exclusion** (e.g., a `.env.example` you actually do
  want synced). `obris sync include .env.example -p <dir>`. Last-call-wins;
  the inverse is `obris sync exclude`.

## Install the plugin

The same plugin works in both surfaces; pick the install path that matches
where you're running Claude.

### In Claude Code (the terminal CLI)

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

### In Cowork (desktop or web)

Cowork doesn't ship a discoverable marketplace, so you add the Obris
marketplace by hand the first time:

1. Open **Customize** in Cowork.
2. Click the **+** next to **Personal Plugins** and choose **Add Marketplace**.
3. Paste `obris-dev/obris-claude-plugin` (a GitHub `owner/repo`, or the
   full git URL) and click **Sync**.

## Enable the MCP server (one-time auth)

The plugin registers Obris's MCP server with Claude, but you still have
to authorize it once before tool calls work. The flow differs between
Claude Code and Cowork.

### In Claude Code

After installing the plugin and running `/reload-plugins`, open the MCP
manager:

```
/mcp
```

Pick **obris** from the list and follow the sign-in prompt. Authorize
Obris in your browser, then return to the session. Every future Obris
tool call uses your account.

### In Cowork

Open **Settings → Connectors**. Find the **Obris** custom connector and
click **Connect**. Authorize Obris in your browser, then return to
Cowork.

Authorization is per device. A second Claude Code workstation, or
Cowork on another machine, walks through the same step.

## CLI authentication

The sync, add, and link skills shell out to the bundled `obris` CLI. The
first time the agent runs an `obris` command, the
[`/obris:auth`](skills/auth/SKILL.md) skill walks you through a two-step
browser sign-in. No API keys to copy.

The MCP server works without ever invoking the CLI. The CLI is only
involved for the disk-level sync commands.

## Troubleshooting

### 403 when the plugin calls the CLI

Claude Cowork sandboxes network access. If calling `/obris:auth` (or
any other obris command) from inside the plugin returns a 403, add the
Obris domains to the allowlist:

1. Open Cowork **Settings → Capabilities → Domain allowlist**
2. Add:
   - `*.obris.ai`
   - `obris-uploads.s3.amazonaws.com`
3. Retry the command

## Links

- [Obris](https://obris.ai)
- [Plugin documentation](https://docs.obris.ai/plugins/claude)
- [Obris CLI on PyPI](https://pypi.org/project/obris-cli/)
- [CLI documentation](https://docs.obris.ai/cli)
- Using Codex instead? See the [Obris Codex Plugin](https://docs.obris.ai/plugins/codex).

---
name: auth
description: Set up Obris CLI authentication. Use when the user needs to connect their Obris account.
disable-model-invocation: true
---

# Authenticate Obris CLI

## Instructions

Log the user into the Obris CLI. Authentication is browser-based — no API
keys to copy or paste. The login is a two-step flow so you can relay the URL
to the user without hanging on a polling loop.

### Step 1 — start the login

```bash
obris auth login --no-wait
```

This creates a session, prints a URL, attempts to open the user's browser,
stores the pending session_id locally, and exits immediately. **Show the
user the URL that was printed** and ask them to open it and authorize the
CLI.

Never run `obris auth login` without `--no-wait` in this skill — the bare
command blocks on a polling loop and will hang indefinitely without the
user ever seeing the URL.

### Step 2 — finalize once the user has authorized

When the user tells you they have authorized in the browser, run:

```bash
obris auth complete
```

This checks the pending session once, saves the tokens, and reports the
logged-in email. If the user hasn't actually finished authorizing, it
prints `"Login still pending"` and exits non-zero — ask the user to
finish and run `obris auth complete` again.

### Check status (read-only)

```bash
obris auth status
```

Reports whether the active environment is authenticated. Does not mutate
any state. Safe to call at any time.

### Log out

```bash
obris auth logout
```

### For local development

```bash
obris --env local auth login --no-wait
obris --env local auth complete
```

### Verify

After `auth complete`, verify it works:

```bash
obris topic list
```

### Prerequisites

The `obris` CLI must be installed. The PyPI package is **`obris-cli`**
(not `obris` — that's a different, older package):

```bash
pip install obris-cli
```

The installed command is still `obris`.

### Troubleshooting: 403 on login

Claude Code sandboxes network access. If `obris auth login --no-wait`
or `obris auth complete` returns a 403, the Obris domains are not on
the host allowlist. Tell the user to:

1. Open Claude Code **Settings → Capabilities → Domain allowlist**
2. Add `*.obris.ai` and `obris-uploads.s3.amazonaws.com`
3. Retry the command

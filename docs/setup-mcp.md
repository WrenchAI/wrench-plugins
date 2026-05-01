# Connect Wrench.ai to Claude — Complete Setup Guide

> Connect your Wrench.ai workspace to Claude on any surface: Claude Code (CLI + desktop app), Claude.ai Cowork/Chat, Cursor, and Windsurf. Once connected, Claude can pull live lead scores, enrichment data, personas, competitor intelligence, and campaign analytics directly into your conversations.

---

## Before you start

**Get your Wrench.ai workspace API key**

1. Go to [web.wrench.ai/api-key](https://web.wrench.ai/api-key)
2. Copy your workspace MCP key — it looks like `wrench_live_xxxx...`

Keep this key private. Never commit it to a repository.

**Wrench.ai MCP endpoint**

```
https://api.v2.wrench.ai/mcp/sse
```

All connections use the `x-api-key` header for authentication.

---

## Claude Code CLI (Recommended)

Claude Code is the terminal-based Claude interface. This is the fastest way to connect.

### One-command setup

```bash
claude mcp add --transport sse wrench https://api.v2.wrench.ai/mcp/sse \
  --header "x-api-key: YOUR_WRENCH_API_KEY"
```

This stores the config at the user scope — available in all your projects automatically.

### Share with your whole team (project scope)

To commit the MCP config so every team member gets it automatically:

```bash
claude mcp add --transport sse --scope project wrench https://api.v2.wrench.ai/mcp/sse \
  --header "x-api-key: YOUR_WRENCH_API_KEY"
```

This writes to `.mcp.json` in your project root. **Important:** Add `.mcp.json` to `.gitignore` if it will contain the raw API key, or use an environment variable instead:

```bash
claude mcp add --transport sse --scope project wrench https://api.v2.wrench.ai/mcp/sse \
  --header "x-api-key: ${WRENCH_API_KEY}"
```

Then each team member sets `WRENCH_API_KEY` in their shell profile.

### Verify the connection

```bash
# Check server status
claude mcp list

# In a Claude Code session
/mcp
```

You should see `wrench` listed as connected. Then ask:

```
What Wrench.ai workspace am I connected to?
```

Claude will call `get_general_user-info` and confirm your workspace.

### Manual config (alternative to CLI)

Edit `~/.claude/settings.json` directly:

```json
{
  "mcpServers": {
    "wrench": {
      "type": "sse",
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

---

## Claude Desktop App (macOS / Windows)

The Claude Desktop app is the standalone app from Anthropic — separate from Claude Code. It supports MCP via a local config file.

### macOS

Edit (or create) `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "wrench": {
      "type": "sse",
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

Then **quit and relaunch** Claude Desktop. The MCP server will connect on startup.

**Quick open from Terminal:**

```bash
open ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

### Windows

Edit (or create) `%APPDATA%\Claude\claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "wrench": {
      "type": "sse",
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

**Quick open from PowerShell:**

```powershell
notepad "$env:APPDATA\Claude\claude_desktop_config.json"
```

Quit and relaunch Claude Desktop after saving.

### Verify

In Claude Desktop, start a new conversation and type:

```
What Wrench.ai workspace am I connected to?
```

If the MCP server connected correctly, Claude will return your workspace name and user info.

---

## Claude.ai Cowork / Chat (Web)

Claude.ai's Cowork interface supports MCP integrations that persist across your Projects.

### Steps

1. Go to [claude.ai](https://claude.ai) and sign in
2. Click your avatar (top right) → **Settings** → **Integrations** → **MCP Servers**
3. Click **Add server**
4. Fill in:
   - **Name:** `Wrench.ai`
   - **URL:** `https://api.v2.wrench.ai/mcp/sse`
   - **Auth type:** Header
   - **Header name:** `x-api-key`
   - **Header value:** `YOUR_WRENCH_API_KEY`
5. Click **Save** and then **Connect**

The Wrench.ai MCP will now be available in all your Claude.ai Projects and Cowork sessions.

### Using with a Project

To make the MCP available only in a specific Project:

1. Open the Project → **Settings** → **Integrations**
2. Enable the Wrench.ai MCP server you just added

### Verify

In any Claude.ai conversation:

```
What Wrench.ai workspace am I connected to?
```

---

## Claude Code IDE Extensions

### VS Code

If you're using the Claude Code extension for VS Code, add the MCP to your VS Code `settings.json`:

```json
{
  "claude-code.mcpServers": {
    "wrench": {
      "type": "sse",
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

Or run the CLI command above — VS Code and the CLI share the same user-scope MCP config.

### JetBrains (IntelliJ, WebStorm, PyCharm)

The Claude Code JetBrains plugin uses the same user-scope config as the CLI. Run the `claude mcp add` command above and it will be available in your JetBrains IDE automatically.

---

## Cursor

In Cursor's MCP config at `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "wrench": {
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

On Windows, this file is at `%USERPROFILE%\.cursor\mcp.json`.

---

## Windsurf

In Windsurf's MCP config at `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "wrench": {
      "serverType": "sse",
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

---

## Environment reference

| Environment | MCP SSE URL |
|---|---|
| **Production** | `https://api.v2.wrench.ai/mcp/sse` |
| QA | `https://api.qa.wrench.ai/mcp/sse` |
| Dev | `https://api.dev.wrench.ai/mcp/sse` |

Use Production unless Wrench.ai support has directed you to a different environment.

---

## What you can do once connected

With the Wrench.ai MCP active, you can ask Claude things like:

```
What's the lead score for john@acme.com?
```

```
Show me the top 10 prospects in my workspace by lead score.
```

```
Build a dossier for my meeting with Jane Smith at Acme Corp tomorrow.
```

```
What personas are most common in my workspace?
```

```
What are my competitors running on Meta right now?
```

```
Generate a personalized email for [contact] based on their Wrench.ai profile.
```

---

## Troubleshooting

**Claude says the tool is unavailable**
- Run `/mcp` in Claude Code to check server status
- Verify your API key is correct at [web.wrench.ai/api-key](https://web.wrench.ai/api-key)
- Check that the URL is `https://api.v2.wrench.ai/mcp/sse` (not http, no trailing slash)

**Connection timeout on startup**
- Run `MCP_TIMEOUT=15000 claude` to extend the startup timeout to 15 seconds

**Wrong workspace data**
- Per MCP connector isolation rules, each Wrench.ai MCP key is scoped to one workspace
- Run `get_general_user-info` to confirm which workspace is connected before any data calls
- Never mix data across connectors if you have multiple workspace keys configured

**Claude Desktop shows server as disconnected**
- Quit Claude Desktop completely (not just close the window) and relaunch
- Confirm the `claude_desktop_config.json` is valid JSON with no trailing commas

---

## Security notes

- Your Wrench.ai API key grants read/write access to your workspace. Never commit it to a public repository.
- If you need to share MCP config with teammates, use environment variable substitution (`${WRENCH_API_KEY}`) and have each person set the variable locally.
- To rotate your key, generate a new one at [web.wrench.ai/api-key](https://web.wrench.ai/api-key) and update your config.

---

*Need help? [Contact Wrench.ai support](https://wrench.ai) or open an issue in this repository.*

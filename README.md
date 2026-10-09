<p align="center">
  <a href="https://geniffy.com"><img src="https://geniffy.com/brand/geniffy-lockup-ink.png" alt="Geniffy" height="44"></a>
</p>

# Geniffy MCP server

Your memory inside Claude, ChatGPT, Cursor, VS Code, Codex and any app that speaks MCP. Tell one of them
something, and every app you connect can find it, with the line it came from and the date it was said.

[![Docs](https://img.shields.io/badge/docs-docs.geniffy.com%2Fmcp-1A1814?style=flat-square)](https://docs.geniffy.com/mcp)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://vscode.dev/redirect/mcp/install?name=geniffy&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.geniffy.com%2Fmcp%22%7D)
[![Add to Cursor](https://img.shields.io/badge/Cursor-Add_to_Cursor-000000?style=flat-square)](https://docs.geniffy.com/mcp/cursor)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)

```text
https://api.geniffy.com/mcp
```

A hosted, remote MCP server (Streamable HTTP) with sign-in built in: there is nothing to install and no key to
copy. Add the URL to your app, sign in to Geniffy when it asks, and choose what the app may do.

Listed in the [official MCP Registry](https://registry.modelcontextprotocol.io/v0/servers?search=io.github.Geniffy/geniffy-mcp)
as `io.github.Geniffy/geniffy-mcp`.

## Connect your app

| App | How |
| --- | --- |
| **Claude** (Claude.ai and the desktop app) | **Settings → Connectors → Add custom connector.** Name it `Geniffy`, paste the URL, leave the OAuth fields empty, then **Connect** and **Allow**. [Guide](https://docs.geniffy.com/mcp/claude) |
| **ChatGPT** (web, developer mode) | **Settings → Plugins → Developer mode**, then **Browse plugins → +**: name `Geniffy`, **Server URL**, paste the URL, **Create**, **Sign in with Geniffy**. [Guide](https://docs.geniffy.com/mcp/chatgpt) |
| **Claude Code** | The [Geniffy plugin](https://github.com/Geniffy/geniffy-claude-code) recalls and saves by itself: `/plugin marketplace add Geniffy/geniffy-claude-code`, `/plugin install geniffy@geniffy`, `/geniffy:login`. Or the server alone: `claude mcp add --transport http geniffy https://api.geniffy.com/mcp`. [Guide](https://docs.geniffy.com/mcp/claude-code) |
| **Cursor** | One click from the [docs](https://docs.geniffy.com/mcp/cursor), or the config below. Cursor asks you to sign in the first time it uses Geniffy. |
| **VS Code** | The **Install in VS Code** badge above, or the config below. VS Code opens the sign-in when it starts the server. [Guide](https://docs.geniffy.com/mcp/vscode) |
| **Codex** | `codex mcp add geniffy --url https://api.geniffy.com/mcp`, then `codex mcp login geniffy`. [Guide](https://docs.geniffy.com/mcp/codex) |
| **Anything else** | Add the URL as a remote HTTP server. Apps that support OAuth open the sign-in by themselves. [Guide](https://docs.geniffy.com/mcp/other-clients) |

Cursor, in `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "geniffy": { "url": "https://api.geniffy.com/mcp" }
  }
}
```

VS Code, in `.vscode/mcp.json`:

```json
{
  "servers": {
    "geniffy": { "type": "http", "url": "https://api.geniffy.com/mcp" }
  }
}
```

## Tools

| Tool | What it does | Changes your memory |
| --- | --- | --- |
| `briefing` | Where a project stands, what is due, the rules that apply and what happened, to start a session with | No |
| `search` | Finds the facts that match, with where and when each was said | No |
| `fetch` | Opens one memory: the exact line it came from, and what it said before | No |
| `ask` | Answers from your memory only, or says that nothing supports an answer | No |
| `profile` | What is lastingly true about you, and what is going on now | No |
| `list_memories` | Your memories, newest first, by kind | No |
| `remember` | Saves something you ask it to remember, or a session as it goes, tool calls included | Yes |
| `correct` | Marks a memory wrong and saves what is right in its place | Yes |
| `forget` | Forgets one memory, or one source, for good | Yes |

Every tool has an input schema, an output schema and annotations that say whether it changes your memory.
`search` and `fetch` return the shape ChatGPT's connectors read. Apps are told to read the briefing at the start
of a session and whenever the task changes. Inputs and results:
[docs.geniffy.com/mcp/tools](https://docs.geniffy.com/mcp/tools).

## How it answers

- **Where you left off.** The briefing says where each project stands, what is due, the rules that apply and
  what happened before, so a new session picks up where the last one ended.
- **With its source.** Every fact comes back with the line it was read from, who said it and when.
- **Or not at all.** When nothing in your memory supports an answer, Geniffy says so, and the app is told to
  say it doesn't know rather than guess.
- **With today's truth.** Facts carry dates. When something changes, the newer fact replaces the older one.

## What you control

- **You approve each app.** Geniffy shows who is asking and where it will send you back. Nothing is shared
  until you press **Allow**.
- **Read, or read and change.** Turn off saving when you connect, and the app never sees the tools that
  change your memory.
- **Disconnect at any time** in the [Geniffy app](https://geniffy.com/app/apps) under **Connect**. Every
  call an app makes is listed under **Requests**, with its name.
- **Yours to download or erase.** Download a copy of everything it holds from the Geniffy app, and erase any of
  it whenever you like.

## Sign-in, for client developers

OAuth 2.1, following the MCP authorization specification: a `401` with `WWW-Authenticate` names the
protected-resource metadata (RFC 9728), which points to the authorization server metadata (RFC 8414).
Dynamic client registration (RFC 7591), PKCE with `S256` only, scopes `memory:read` and `memory:write`,
hourly access tokens, rotating refresh tokens, revocation (RFC 7009) and the `iss` response parameter
(RFC 9207). Details: [docs.geniffy.com/mcp/sign-in](https://docs.geniffy.com/mcp/sign-in).

```text
https://api.geniffy.com/.well-known/oauth-protected-resource/mcp
https://api.geniffy.com/.well-known/oauth-authorization-server
```

## Without a browser

Scripts, CI and agents that cannot open a browser can send a Geniffy API key as a header. A key reaches
your app's [spaces](https://docs.geniffy.com/keys-and-spaces) too: every tool then takes an optional `space`.

```bash
claude mcp add --transport http geniffy https://api.geniffy.com/mcp \
  --header "Authorization: Bearer $GENIFFY_API_KEY"
```

## Build your own app on Geniffy

The MCP server gives the AI apps you use your memory. To give your own app's users a memory, use the API:

- Python: [`pip install geniffy`](https://github.com/Geniffy/geniffy-python)
- TypeScript and JavaScript: [`npm install geniffy`](https://github.com/Geniffy/geniffy-typescript)
- Runnable examples: [Geniffy/examples](https://github.com/Geniffy/examples)
- Docs: [docs.geniffy.com](https://docs.geniffy.com)

## Security

Report a vulnerability to ops@geniffy.com. Please don't open a public issue for it.

# NukeBG MCP server

NukeBG runs a remote [Model Context Protocol](https://modelcontextprotocol.io) server, so AI assistants and agents (Claude, ChatGPT, Cursor, VS Code, Windsurf and others) can remove backgrounds, remove objects and upscale images with your NukeBG credits.

- URL: `https://api.nukebg.com/mcp`
- Transport: Streamable HTTP (stateless, JSON responses)
- Auth: sign in with OAuth (no key to copy), or your NukeBG API key in the `X-Api-Key` header (or `Authorization: Bearer <key>`)
- Docs: https://nukebg.com/mcp-server
- API reference: https://nukebg.com/docs

## Tools

| Tool | What it does | Credits |
|---|---|---|
| `remove_background` | Transparent cutout, or the subject on white, a colour, gradient or built-in scene, at an optional marketplace size | 1 (HD 2, Ultra 4) |
| `erase_objects` | Removes what you describe ("the cables on the desk"); the rest stays pixel for pixel | 25 (enhance 40) |
| `upscale_image` | 2× or 4× Real-ESRGAN upscale | 2 or 4 |
| `get_credits` | Balance and plan | free |

Inputs are a public `image_url` or `image_base64`. Results come back as a download link valid for 24 hours, plus the image inline when it's under 1.5 MB. Failed calls are never charged.

## Sign in with OAuth

Clients that support MCP authorization connect with just the URL and open a NukeBG sign-in page, so there is no key to copy:

- **Claude** (claude.ai, Desktop, mobile): Customize > Connectors > Add custom connector, URL `https://api.nukebg.com/mcp`, then Connect.
- **ChatGPT** (developer mode): create an app with the URL and OAuth authentication.
- **Claude Code**: `claude mcp add --transport http nukebg https://api.nukebg.com/mcp`, then `/mcp` > Authenticate.
- **Cursor, VS Code**: add the URL without headers; they prompt you to sign in.

The server follows the [MCP authorization spec](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization): protected resource metadata at `https://api.nukebg.com/.well-known/oauth-protected-resource/mcp`, authorization server `https://nukebg.com` (PKCE S256, Client ID Metadata Documents and dynamic client registration), tokens bound to the MCP resource. Tool calls spend the signed-in account's credits. Disconnect apps on your [account page](https://nukebg.com/account).

## Connect with an API key

Create an API key at https://nukebg.com/account first.

**Claude Code plugin** (MCP server plus the `nukebg-images` skill)

This repo is a Claude Code plugin marketplace. Install it, then run `/mcp` and choose Authenticate to sign in to NukeBG (OAuth, no key to copy):

```bash
claude plugin marketplace add nukebg/nukebg-mcp
claude plugin install nukebg@nukebg
```

**Claude Code** (MCP server only)

```bash
claude mcp add --transport http nukebg https://api.nukebg.com/mcp --header "X-Api-Key: YOUR_KEY"
```

**Gemini CLI** (extension; signs in to NukeBG with OAuth on first use)

```bash
gemini extensions install https://github.com/nukebg/nukebg-mcp
```

**Cursor** (`~/.cursor/mcp.json`) and **Windsurf**

```json
{
  "mcpServers": {
    "nukebg": {
      "url": "https://api.nukebg.com/mcp",
      "headers": { "X-Api-Key": "YOUR_KEY" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "nukebg": {
      "type": "http",
      "url": "https://api.nukebg.com/mcp",
      "headers": { "X-Api-Key": "${input:nukebg-key}" }
    }
  },
  "inputs": [{ "id": "nukebg-key", "type": "promptString", "description": "NukeBG API key", "password": true }]
}
```

**Claude Desktop** (`claude_desktop_config.json`, through the `mcp-remote` bridge)

```json
{
  "mcpServers": {
    "nukebg": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.nukebg.com/mcp", "--header", "X-Api-Key:YOUR_KEY"]
    }
  }
}
```

## Agent skill

[`skills/nukebg-images/SKILL.md`](skills/nukebg-images/SKILL.md) teaches an agent when and how to use NukeBG, with or without the MCP server (it falls back to the REST API). It uses only the standard `name`, `description`, `license` and `compatibility` fields, so the same folder works as a Claude Code skill and as an uploaded skill on claude.ai or the Claude API.

## Test it

```bash
curl -s https://api.nukebg.com/mcp -H "content-type: application/json" -H "X-Api-Key: YOUR_KEY" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_credits","arguments":{}}}'
```

Or run the MCP Inspector: `npx @modelcontextprotocol/inspector`, choose Streamable HTTP and enter the URL. Call a tool and it walks you through the NukeBG sign-in; or add the `X-Api-Key` header instead.

## About

NukeBG is a background remover for online sellers and developers, made by VOLODS LTD (England and Wales). New accounts get 50 free credits with no watermark, and failed calls are never charged. The server is hosted; there is nothing to install. Questions: nukebg@volods.com.

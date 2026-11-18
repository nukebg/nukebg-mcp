# Installing the NukeBG MCP server

NukeBG is a hosted (remote) MCP server. There is nothing to download or build, and no API key is needed to install it.

## 1. Add the server (no key needed)

Add this entry to the MCP settings as is:

```json
{
  "mcpServers": {
    "nukebg": {
      "type": "streamableHttp",
      "url": "https://api.nukebg.com/mcp"
    }
  }
}
```

The server connects and lists its tools without credentials, so the install is complete at this point.

## 2. Sign in the first time a tool runs

Tool calls need a NukeBG account. The first call returns `401` with an OAuth challenge:

- Clients that support MCP OAuth open the NukeBG sign-in page. The user signs in (or signs up; new accounts get 50 free credits) and allows access. There is no key to copy.
- If the client doesn't support MCP OAuth, ask the user for a NukeBG API key (created at https://nukebg.com/account) and add it as a header to the same entry:

```json
"headers": { "X-Api-Key": "THE_USER'S_KEY" }
```

To check it works, call `get_credits`. It returns the credit balance and costs nothing.

## Tools

`remove_background`, `erase_objects`, `upscale_image`, `get_credits`. Pass images as a public `image_url` (NukeBG downloads it) or as `image_base64`. Results come back as a download link valid for 24 hours, plus the image inline when it's under 1.5 MB.

## Stdio-only clients

If the client only supports stdio servers, use the `mcp-remote` bridge, which also handles the OAuth sign-in:

```json
{
  "mcpServers": {
    "nukebg": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.nukebg.com/mcp"]
    }
  }
}
```

# Installing the NukeBG MCP server

NukeBG is a hosted (remote) MCP server. There is nothing to download or build.

If the client supports MCP OAuth (remote servers with sign-in), add `https://api.nukebg.com/mcp` with no headers and the user signs in to NukeBG when a tool is first used. Otherwise use an API key:

1. Ask the user for their NukeBG API key. If they don't have one, they can create it at https://nukebg.com/account (new accounts get 50 free credits).
2. Add this server to the MCP settings, replacing YOUR_KEY with the key:

```json
{
  "mcpServers": {
    "nukebg": {
      "type": "streamableHttp",
      "url": "https://api.nukebg.com/mcp",
      "headers": { "X-Api-Key": "YOUR_KEY" }
    }
  }
}
```

3. Confirm it works by calling the `get_credits` tool. It returns the credit balance and costs nothing.

Tools: `remove_background`, `erase_objects`, `upscale_image`, `get_credits`. Images are passed as a public `image_url` or as `image_base64`. Results come back as a download link valid for 24 hours, plus the image inline when it's under 1.5 MB.

If the client only supports stdio servers, use the `mcp-remote` bridge instead:

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

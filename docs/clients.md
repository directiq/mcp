# DirectIQ MCP: client setup

Endpoint: `https://mcp.directiq.com/mcp` · Header: `x-api-key: YOUR_DIRECTIQ_API_KEY`

## Client setup

### Cursor
[Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=directiq&config=eyJ1cmwiOiJodHRwczovL21jcC5kaXJlY3RpcS5jb20vbWNwIiwiaGVhZGVycyI6eyJ4LWFwaS1rZXkiOiJZT1VSX0RJUkVDVElRX0FQSV9LRVkifX0=). This link opens Cursor with the config filled in. Replace `YOUR_DIRECTIQ_API_KEY` with your key.

Or add it manually to `~/.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "directiq": {
      "url": "https://mcp.directiq.com/mcp",
      "headers": { "x-api-key": "YOUR_DIRECTIQ_API_KEY" }
    }
  }
}
```

### VS Code (GitHub Copilot)
Add to `.vscode/mcp.json`. VS Code prompts for the key and stores it securely:
```json
{
  "inputs": [
    { "type": "promptString", "id": "directiq_api_key", "description": "DirectIQ API key", "password": true }
  ],
  "servers": {
    "directiq": {
      "type": "http",
      "url": "https://mcp.directiq.com/mcp",
      "headers": { "x-api-key": "${input:directiq_api_key}" }
    }
  }
}
```

### Claude Code
```bash
claude mcp add --transport http directiq https://mcp.directiq.com/mcp --header "x-api-key: YOUR_DIRECTIQ_API_KEY"
```

### Claude Desktop
Until OAuth ships, use the `mcp-remote` bridge in `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "directiq": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.directiq.com/mcp", "--header", "x-api-key:${DIRECTIQ_API_KEY}"],
      "env": { "DIRECTIQ_API_KEY": "YOUR_DIRECTIQ_API_KEY" }
    }
  }
}
```

### Windsurf
`~/.codeium/windsurf/mcp_config.json`:
```json
{
  "mcpServers": {
    "directiq": {
      "serverUrl": "https://mcp.directiq.com/mcp",
      "headers": { "x-api-key": "YOUR_DIRECTIQ_API_KEY" }
    }
  }
}
```

### OpenAI Codex
`~/.codex/config.toml`, after setting `export DIRECTIQ_API_KEY=...`:
```toml
[mcp_servers.directiq]
url = "https://mcp.directiq.com/mcp"
env_http_headers = { "x-api-key" = "DIRECTIQ_API_KEY" }
```

### Any other MCP client
| Setting | Value |
|---|---|
| Transport | Streamable HTTP |
| URL | `https://mcp.directiq.com/mcp` |
| Header | `x-api-key: <your DirectIQ API key>` |

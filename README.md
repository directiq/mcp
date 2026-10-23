# DirectIQ MCP Server

[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-com.directiq.mcp%2Fdirectiq-blue)](https://registry.modelcontextprotocol.io/v0.1/servers?search=com.directiq.mcp/directiq)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Connect Claude, ChatGPT, Cursor, VS Code, Windsurf, and any other [Model Context Protocol](https://modelcontextprotocol.io) client to your **DirectIQ** account. Manage contacts, lists, and tags, get an account overview, and send transactional email in plain language.

DirectIQ is an email marketing and transactional email platform for small businesses, with fast human support and pay-as-you-go pricing. → [directiq.com](https://www.directiq.com)

- **Endpoint:** `https://mcp.directiq.com/mcp`
- **Transport:** Streamable HTTP (remote, hosted by DirectIQ, nothing to install)
- **Auth:** DirectIQ API key in the `x-api-key` header, or `Authorization: Bearer <key>` (OAuth coming soon)
- **Server card:** `https://mcp.directiq.com/.well-known/mcp/server-card.json`
- **Registry name:** `com.directiq.mcp/directiq`

> This repository holds the public documentation, registry metadata, and client configuration for the hosted DirectIQ MCP server at `mcp.directiq.com`, operated by DirectIQ. There is nothing to install or build.

## Quick start

1. **Get an API key:** log in to the [DirectIQ dashboard](https://app.directiq.com), then go to **Settings → API Keys** and copy or generate a key. No account yet? [Sign up free](https://app.directiq.com/identity/signup/start).
2. **Add the server to your client:** see the snippets below.
3. **Try a prompt:** *"Give me an overview of my DirectIQ account."*

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

## Tools

| Tool | What it does | Annotations |
|---|---|---|
| `directiq_account_overview` | Summarizes the account: total contacts, active/disabled counts, list count, tag count, and confirmed sender addresses | read-only |
| `directiq_contact_count` | Returns the total number of contacts | read-only |
| `directiq_list_contacts` | Lists contacts, paginated (`page`, `pageSize` up to 500) | read-only |
| `directiq_get_contact` | Gets a single contact by numeric ID | read-only |
| `directiq_update_contact` | Updates a contact's fields (e.g. firstName, lastName, email) | **destructive**, idempotent |
| `directiq_list_lists` | Lists all contact lists | read-only |
| `directiq_get_list` | Gets a contact list by numeric ID | read-only |
| `directiq_create_list` | Creates a new contact list | write |
| `directiq_list_tags` | Lists all tags | read-only |
| `directiq_get_tag` | Gets a tag by numeric ID | read-only |
| `directiq_create_tag` | Creates a new tag | write |
| `directiq_send_email` | Sends one transactional email (from a confirmed sender; to, subject, HTML or text content, cc/bcc) | write, open-world |
| `directiq_list_endpoints` | Lists known DirectIQ API operations, with optional search | read-only |
| `directiq_read_request` | Authenticated GET to any DirectIQ API endpoint | read-only |
| `directiq_write_request` | Authenticated POST/PUT/PATCH/DELETE to any DirectIQ API endpoint; may modify or permanently delete data | **destructive** |

Every tool declares MCP annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`) so your client can ask for confirmation before changes. Full API schemas: `https://rest.directiq.com/{core|contacts|subscription}/swagger/index.html`.

## Documentation

- [Client setup](docs/clients.md)
- [Tool reference](docs/tools.md): every tool with its inputs and annotations
- [Authentication and security](docs/security.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Instructions for AI installers (Cline)](llms-install.md)

## Example prompts

- "Give me an overview of my DirectIQ account: contacts, lists, tags, and confirmed senders."
- "How many contacts do I have, and how many are disabled?"
- "Create a list called 'Fall Promo 2026' and a tag called 'vip'."
- "Show me contact 12345 and change their first name to Jamie."
- "Send a transactional email from hello@mybusiness.com to me@mybusiness.com with the subject 'Order confirmed' and a short thank-you message."
- "What DirectIQ API endpoints are available for subscriptions?"

## Security and data handling

- Your API key is sent only as the `x-api-key` header over HTTPS to `mcp.directiq.com`. Never paste it into chat or commit it to a repository.
- The server acts with the permissions of the API key's DirectIQ account. Revoke or rotate keys anytime in **Settings → API Keys**.
- Emails can only be sent from confirmed sender addresses on your account.
- `directiq_write_request` and `directiq_update_contact` can change or delete data. Keep your client's tool-approval prompts on.
- Privacy policy: https://www.directiq.com/privacy-policy · Terms: https://www.directiq.com/terms-of-service

## Pricing

The MCP server is free to use. Emails sent through it count toward your normal DirectIQ plan, including pay-as-you-go. See [directiq.com/pricing](https://www.directiq.com/pricing).

## Support

- Help center: https://help.directiq.com
- Email: support@directiq.com
- Setup guide: https://www.directiq.com/mcp-setup/
- Issues with this repo or listing: [GitHub Issues](https://github.com/directiq/mcp/issues)

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

## License

The documentation and configuration in this repository are licensed under the [MIT License](LICENSE). The DirectIQ service itself is governed by the [DirectIQ Terms of Service](https://www.directiq.com/terms-of-service).

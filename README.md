# DirectIQ MCP Server

[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-com.directiq.mcp%2Fdirectiq-blue)](https://registry.modelcontextprotocol.io/v0.1/servers?search=com.directiq.mcp/directiq)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![DirectIQ MCP connector](https://glama.ai/mcp/connectors/com.directiq.mcp/directiq/badges/score.svg)](https://glama.ai/mcp/connectors/com.directiq.mcp/directiq)

Connect Claude, ChatGPT, Cursor, VS Code, Windsurf, and any other [Model Context Protocol](https://modelcontextprotocol.io) client to your **DirectIQ** account. Manage contacts, lists, and tags, see how your campaigns performed, and send transactional email in plain language.

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
[Add to Cursor](https://www.directiq.com/mcp/cursor/). This link opens Cursor with the config filled in. Replace `YOUR_DIRECTIQ_API_KEY` with your key.

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

30 tools, grouped by what they work on. Full inputs for each tool are in the [tool reference](docs/tools.md).

| Tool | What it does | Behavior |
|---|---|---|
| **Account** | | |
| `directiq_account_overview` | Summarize the account in one call: total contacts, active/disabled status counts, list count, tag count, and confirmed sender addresses. | read-only |
| **Contacts** | | |
| `directiq_get_contact` | Get one contact by numeric ID: email, first and last name, status, created and updated dates, tags, lists, and notes. | read-only |
| `directiq_list_contacts` | List contacts one page at a time, oldest first, with each contact's ID, email, name, status, tags, and lists. | read-only |
| `directiq_contact_count` | Return the total number of contacts in the account as a single number. | read-only |
| `directiq_upsert_contact` | Add a contact to a list, creating the contact if the email is new or updating its name if it already exists. | **destructive**, idempotent |
| `directiq_update_contact` | Update fields on an existing contact by ID, such as firstName, lastName, or email. | **destructive**, idempotent |
| `directiq_delete_contact` | Permanently delete a contact by ID, removing it from every list and tag. | **destructive**, idempotent |
| **Lists and segments** | | |
| `directiq_list_lists` | List every contact list in the account with its ID and name. | read-only |
| `directiq_get_list` | Get one contact list by ID: name, created and modified dates, and total, active, and disabled contact counts. | read-only |
| `directiq_create_list` | Create a new, empty contact list and return it with its new ID. | write |
| `directiq_rename_list` | Rename a contact list by ID. | **destructive**, idempotent |
| `directiq_delete_list` | Permanently delete a contact list by ID. | **destructive**, idempotent |
| `directiq_add_contact_to_list` | Add an existing contact (by contact ID) to a list. | write, idempotent |
| `directiq_remove_contact_from_list` | Remove a contact from one list. | **destructive**, idempotent |
| `directiq_list_segments` | List saved segments (rule-based groups of contacts) with their IDs and names. | read-only |
| **Tags** | | |
| `directiq_list_tags` | List every tag in the account with its ID and name. | read-only |
| `directiq_get_tag` | Get one tag by ID with its name and details. | read-only |
| `directiq_create_tag` | Create a new tag and return it with its ID. | write |
| `directiq_rename_tag` | Rename a tag by ID. | **destructive**, idempotent |
| `directiq_delete_tag` | Permanently delete a tag by ID and remove it from every contact. | **destructive**, idempotent |
| `directiq_tag_contact` | Apply one or more tags to a contact by contact ID. | write, idempotent |
| `directiq_untag_contact` | Remove a tag from a contact, identified by the contact email and the tag name. | **destructive**, idempotent |
| **Campaigns and results** | | |
| `directiq_list_campaigns` | List email campaigns and automation flows with ID, name, type (Regular or Flow), status, subject, sender, target lists, recipient count, and scheduled send time. | read-only |
| `directiq_get_campaign` | Get one campaign by ID: name, type, status, subject, sender, target lists and segments, template ID, recipient count, and scheduled send time. | read-only |
| `directiq_campaign_stats` | Summarize how a campaign performed: counts of recipients, opens, clicks, bounces, unsubscribes, and complaints, plus open, click, bounce, and unsubscribe rates as percentages of recipients. | read-only |
| `directiq_campaign_report` | List the contacts behind one campaign result, one page at a time: who received, opened, clicked, bounced, unsubscribed, or complained. | read-only |
| **Transactional email** | | |
| `directiq_send_email` | Send one transactional email (a receipt, confirmation, or one-to-one message) immediately. | write, open-world |
| **Any other endpoint** | | |
| `directiq_list_endpoints` | List known DirectIQ API operations (HTTP method, API group, path, summary). | read-only |
| `directiq_read_request` | Issue an authenticated GET request to any DirectIQ endpoint and return the JSON response. | read-only |
| `directiq_write_request` | Issue an authenticated POST, PUT, PATCH, or DELETE request to any DirectIQ endpoint. | **destructive** |

Every tool declares MCP annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`) so your client can ask for confirmation before changes. Full API schemas: `https://rest.directiq.com/{core|contacts|subscription}/swagger/index.html`.

## Documentation

- [Client setup](docs/clients.md)
- [Tool reference](docs/tools.md): every tool with its inputs and annotations
- [Authentication and security](docs/security.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Instructions for AI installers (Cline)](llms-install.md)

## Example prompts

- "Give me an overview of my DirectIQ account: contacts, lists, tags, and confirmed senders."
- "How did my last campaign do? Show open, click, and unsubscribe rates."
- "Who clicked in the 'Fall Promo' campaign?"
- "Add jamie@example.com to my Newsletter list as Jamie Lee and tag them 'vip'."
- "Create a list called 'Fall Promo 2026', then move everyone tagged 'vip' into it."
- "Send a transactional email from hello@mybusiness.com to me@mybusiness.com with the subject 'Order confirmed' and a short thank-you message."

## Security and data handling

- Your API key is sent only as the `x-api-key` header over HTTPS to `mcp.directiq.com`. Never paste it into chat or commit it to a repository.
- The server acts with the permissions of the API key's DirectIQ account. Revoke or rotate keys anytime in **Settings → API Keys**.
- Emails can only be sent from confirmed sender addresses on your account.
- Tools marked **destructive** above can overwrite or permanently delete data (for example `directiq_delete_contact`, `directiq_delete_list`, and `directiq_write_request`). Keep your client's tool-approval prompts on, and only auto-approve read-only tools.
- Tool calls without an API key are rejected with HTTP 401; listing the tools works without one.
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

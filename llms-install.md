# Installing the DirectIQ MCP server (instructions for AI agents such as Cline)

The DirectIQ MCP server is a **hosted remote server**. Do not clone, build, or run anything locally.

1. Ask the user for their DirectIQ API key (DirectIQ dashboard → Settings → API Keys). Do not invent one and do not write it to any file other than the MCP settings file.
2. Add this entry to the MCP settings file (for Cline: `cline_mcp_settings.json`):
```json
{
  "mcpServers": {
    "directiq": {
      "type": "streamableHttp",
      "url": "https://mcp.directiq.com/mcp",
      "headers": { "x-api-key": "USER_PROVIDED_API_KEY" },
      "disabled": false,
      "autoApprove": ["directiq_account_overview", "directiq_contact_count", "directiq_list_lists", "directiq_list_tags", "directiq_list_endpoints"]
    }
  }
}
```
3. Verify by calling `directiq_account_overview`. A successful result lists contact, list, and tag counts. The error "Missing API key" means the header wasn't sent; re-check step 2.
4. Never auto-approve `directiq_send_email`, `directiq_update_contact`, `directiq_create_list`, `directiq_create_tag`, or `directiq_write_request`. These send email or change account data.

# Troubleshooting

## "Missing API key. Configure your DirectIQ API key in your MCP client settings."

The client connected but didn't send the `x-api-key` header. Check that:

- the header name is exactly `x-api-key` (or use `Authorization: Bearer <key>`);
- your client's config uses the right key for headers (`headers` in Cursor, VS Code and Cline, `serverUrl` plus `headers` in Windsurf, `env_http_headers` in Codex);
- you restarted the client after editing its config.

## Authentication or "401/403" errors from DirectIQ

The key was sent but DirectIQ rejected it. It may be mistyped, revoked, or from another account. Generate a new key in **Settings → API Keys** and update your client.

## "405 Method not allowed"

The server only accepts `POST` on `/mcp` (stateless Streamable HTTP; no SSE stream). Make sure your client uses the **Streamable HTTP** transport, not the older SSE transport. If your client only supports stdio, use the `mcp-remote` bridge shown in the Claude Desktop setup in [clients.md](clients.md).

## Sending email fails

- The `from` address must be a confirmed sender on your account. Ask *"Give me an overview of my DirectIQ account"* to see your confirmed senders.
- The subject and content must each be at least 2 characters.
- Sends count toward your DirectIQ plan; check your plan's sending limits.

## A tool I need is missing

Use `directiq_list_endpoints` to find the DirectIQ API operation, then call it with `directiq_read_request` (GET) or `directiq_write_request` (POST/PUT/PATCH/DELETE). Request and response schemas are in the API docs: [core](https://rest.directiq.com/core/swagger/index.html), [contacts](https://rest.directiq.com/contacts/swagger/index.html), [subscription](https://rest.directiq.com/subscription/swagger/index.html).

You can also [open an issue](https://github.com/directiq/mcp/issues) to request a dedicated tool.

## Still stuck?

- Help center: https://help.directiq.com
- Email: support@directiq.com

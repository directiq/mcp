# Authentication and security

## Authentication

The server uses your DirectIQ API key. OAuth is planned.

1. Log in to the [DirectIQ dashboard](https://app.directiq.com).
2. Go to **Settings → API Keys** and copy a key, or generate a new one.
3. Send it with every request in the `x-api-key` header. `Authorization: Bearer <key>` also works.

`initialize` and `tools/list` work without a key, so clients and directories can see which tools exist. Every `tools/call` needs a key.

## What the key can do

The server acts with the full permissions of the DirectIQ account the key belongs to. It does not store your key: each request is handled on its own (stateless Streamable HTTP), and the key is forwarded to the DirectIQ API over HTTPS only for that request. Keys are redacted from server logs and error messages.

## Keeping your key safe

- Never paste your key into a chat or commit it to a repository.
- Prefer client features that store secrets securely, such as VS Code `inputs` with `"password": true` or environment variables in Codex.
- Use a separate key for each client so you can revoke one without affecting the others.
- Revoke or rotate keys at any time in **Settings → API Keys**. Revoked keys stop working right away.

## Tools that change data

| Tool | Effect |
|---|---|
| `directiq_send_email` | Sends a real email from a confirmed sender on your account |
| `directiq_create_list`, `directiq_create_tag` | Create lists and tags |
| `directiq_update_contact` | Overwrites contact fields |
| `directiq_write_request` | Any POST/PUT/PATCH/DELETE on the DirectIQ API; can permanently delete data |

Every tool declares MCP annotations so clients can ask before running these. Keep your client's tool-approval prompts on, and only auto-approve read-only tools.

Email can only be sent from sender addresses you have confirmed in DirectIQ.

## Policies

- Privacy policy: https://www.directiq.com/privacy-policy
- Terms of service: https://www.directiq.com/terms-of-service

## Reporting a security issue

Email support@directiq.com with "Security" in the subject. Please don't open a public issue for a vulnerability.

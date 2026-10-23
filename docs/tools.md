# Tool reference

The DirectIQ MCP server exposes 15 tools. This page is generated from the server card at `https://mcp.directiq.com/.well-known/mcp/server-card.json` (copy: [`.well-known/mcp/server-card.json`](../.well-known/mcp/server-card.json)).

Each tool declares MCP annotations: `readOnlyHint`, `destructiveHint`, `idempotentHint`, and `openWorldHint`. Clients use them to auto-approve reads and ask before changes.

## `directiq_get_contact`

**Get Contact** · read-only

Get a single contact by its numeric ID.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric contact ID |

## `directiq_list_contacts`

**List Contacts** · read-only

List contacts, optionally paginated.

| Argument | Type | Required | Description |
|---|---|---|---|
| `page` | integer | no | 1-based page number |
| `pageSize` | integer | no | Results per page (max 500) |

## `directiq_contact_count`

**Contact Count** · read-only

Get the total number of contacts in the account.

No arguments.

## `directiq_update_contact`

**Update Contact** · **destructive**, idempotent

Update a contact by ID. Provide the fields to change (e.g. firstName, lastName, email).

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric contact ID |
| `fields` | object | yes | Object of contact fields to update |

## `directiq_list_lists`

**List Contact Lists** · read-only

List all contact lists.

No arguments.

## `directiq_get_list`

**Get Contact List** · read-only

Get a contact list by its numeric ID.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric list ID |

## `directiq_create_list`

**Create Contact List** · write

Create a new contact list.

| Argument | Type | Required | Description |
|---|---|---|---|
| `name` | string | yes | List name (max 200 characters) |

## `directiq_list_tags`

**List Tags** · read-only

List all tags.

No arguments.

## `directiq_get_tag`

**Get Tag** · read-only

Get a tag by its numeric ID.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric tag ID |

## `directiq_create_tag`

**Create Tag** · write

Create a new tag.

| Argument | Type | Required | Description |
|---|---|---|---|
| `name` | string | yes | Tag name |

## `directiq_send_email`

**Send Transactional Email** · write, open-world

Send a transactional email.

| Argument | Type | Required | Description |
|---|---|---|---|
| `from` | string | yes | Sender email address (must be a confirmed sender) |
| `to` | string | yes | Recipient email address |
| `subject` | string | yes | Email subject (min 2 characters) |
| `content` | string | yes | Email body (HTML or text, min 2 characters) |
| `fromName` | string | no | Sender display name |
| `cc` | array of string | no | CC recipients |
| `bcc` | array of string | no | BCC recipients |

## `directiq_account_overview`

**Account Overview** · read-only

Summarize the account: total contacts, active/disabled status counts, list count, tag count, and confirmed sender addresses. Individual sections degrade gracefully if unavailable.

No arguments.

## `directiq_list_endpoints`

**List DirectIQ Endpoints** · read-only

List known DirectIQ API operations (HTTP method, API group, path, summary). Optionally filter by a search term. Call GET operations with directiq_read_request and POST/PUT/PATCH/DELETE operations with directiq_write_request.

| Argument | Type | Required | Description |
|---|---|---|---|
| `search` | string | no | Optional case-insensitive filter matched against each operation path or summary |

## `directiq_read_request`

**DirectIQ Read Request** · read-only

Issue an authenticated GET request to any DirectIQ endpoint. Use directiq_list_endpoints to discover GET operations (group, path) that have no dedicated tool. Full request/response schemas for each group are in the DirectIQ API docs at https://rest.directiq.com/{group}/swagger/index.html (core, contacts, subscription).

| Argument | Type | Required | Description |
|---|---|---|---|
| `group` | string: `core`, `contacts`, `subscription` | yes | DirectIQ API group: core, contacts, or subscription |
| `path` | string | yes | Endpoint path within the group, e.g. '/contact/list' or '/email/send' |
| `query` | object | no | Optional query parameters |

## `directiq_write_request`

**DirectIQ Write Request** · **destructive**

Issue an authenticated POST, PUT, PATCH, or DELETE request to any DirectIQ endpoint. May create, modify, or permanently delete account data. Use directiq_list_endpoints to discover operations (method, group, path) that have no dedicated tool. Full request/response schemas for each group are in the DirectIQ API docs at https://rest.directiq.com/{group}/swagger/index.html (core, contacts, subscription).

| Argument | Type | Required | Description |
|---|---|---|---|
| `group` | string: `core`, `contacts`, `subscription` | yes | DirectIQ API group: core, contacts, or subscription |
| `method` | string: `POST`, `PUT`, `PATCH`, `DELETE` | yes | HTTP method |
| `path` | string | yes | Endpoint path within the group, e.g. '/contact/list' or '/email/send' |
| `query` | object | no | Optional query parameters |
| `body` | object or array | no | Optional JSON request body: an object, or an array for bulk operations |

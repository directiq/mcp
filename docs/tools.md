# Tool reference

The DirectIQ MCP server exposes 30 tools. This page is generated from the server card at `https://mcp.directiq.com/.well-known/mcp/server-card.json` (copy: [`.well-known/mcp/server-card.json`](../.well-known/mcp/server-card.json)).

Each tool declares MCP annotations: `readOnlyHint`, `destructiveHint`, `idempotentHint`, and `openWorldHint`. Clients use them to auto-approve reads and ask before changes.

## `directiq_account_overview`

**Account Overview** · read-only

Summarize the account in one call: total contacts, active/disabled status counts, list count, tag count, and confirmed sender addresses. A good first call in a conversation, and the way to find a valid "from" address for directiq_send_email. Each section that fails to load is returned as { error } without failing the rest.

No arguments.

## `directiq_get_contact`

**Get Contact** · read-only

Get one contact by numeric ID: email, first and last name, status, created and updated dates, tags, lists, and notes. Use when you already have the ID; to find a contact, page through directiq_list_contacts. Returns an error if no contact has that ID.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric contact ID (from directiq_list_contacts or directiq_get_contact) |

## `directiq_list_contacts`

**List Contacts** · read-only

List contacts one page at a time, oldest first, with each contact's ID, email, name, status, tags, and lists. Returns { pageNumber, totalCount, result, hasNextPage }; request the next pageNumber while hasNextPage is true. Use to browse contacts or find an ID. For just the total, use directiq_contact_count instead.

| Argument | Type | Required | Description |
|---|---|---|---|
| `pageNumber` | integer | no | 1-based page number (default 1) |
| `pageSize` | integer (min 50, max 500) | no | Results per page, 50–500 (default 50) |

## `directiq_contact_count`

**Contact Count** · read-only

Return the total number of contacts in the account as a single number. Use for a quick count; for counts plus active/disabled status, lists, tags, and senders, use directiq_account_overview.

No arguments.

## `directiq_upsert_contact`

**Add or Update Contact** · **destructive**, idempotent

Add a contact to a list, creating the contact if the email is new or updating its name if it already exists. Optionally applies tags (created if missing). Use this to create contacts; to change other fields on an existing contact, use directiq_update_contact. Returns import counts (inserted, alreadyExisting, addedToList); emails on a suppression or bounce list are counted but not added.

| Argument | Type | Required | Description |
|---|---|---|---|
| `listId` | integer | yes | Numeric list ID (from directiq_list_lists) |
| `email` | string | yes | Contact email address |
| `firstName` | string | no | First name; overwrites the existing value |
| `lastName` | string | no | Last name; overwrites the existing value |
| `tags` | array of string | no | Tag names to apply; missing tags are created |

## `directiq_update_contact`

**Update Contact** · **destructive**, idempotent

Update fields on an existing contact by ID, such as firstName, lastName, or email. Only the fields you pass change, and they overwrite the current values. To create a contact, use directiq_upsert_contact; to change list or tag membership, use the list and tag tools.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric contact ID (from directiq_list_contacts or directiq_get_contact) |
| `fields` | object | yes | Contact fields to change, e.g. {"firstName": "Jamie"} |

## `directiq_delete_contact`

**Delete Contact** · **destructive**, idempotent

Permanently delete a contact by ID, removing it from every list and tag. This cannot be undone. Confirm with the user first; to only take a contact off one list, use directiq_remove_contact_from_list.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric contact ID (from directiq_list_contacts or directiq_get_contact) |

## `directiq_list_lists`

**List Contact Lists** · read-only

List every contact list in the account with its ID and name. Use to find a list ID before adding contacts, sending campaigns, or calling directiq_get_list.

No arguments.

## `directiq_get_list`

**Get Contact List** · read-only

Get one contact list by ID: name, created and modified dates, and total, active, and disabled contact counts. Use directiq_list_lists to find the ID. Returns an error if no list has that ID.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric list ID (from directiq_list_lists) |

## `directiq_create_list`

**Create Contact List** · write

Create a new, empty contact list and return it with its new ID. Calling it twice creates two lists with the same name, so check directiq_list_lists first. Add contacts afterwards with directiq_upsert_contact or directiq_add_contact_to_list.

| Argument | Type | Required | Description |
|---|---|---|---|
| `name` | string | yes | List name, 1–200 characters |

## `directiq_rename_list`

**Rename Contact List** · **destructive**, idempotent

Rename a contact list by ID. Its contacts are unchanged; the previous name is overwritten.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric list ID (from directiq_list_lists) |
| `name` | string | yes | New name, 1–200 characters |

## `directiq_delete_list`

**Delete Contact List** · **destructive**, idempotent

Permanently delete a contact list by ID. The contacts themselves are kept, but their membership in this list is lost. This cannot be undone; confirm with the user first.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric list ID (from directiq_list_lists) |

## `directiq_add_contact_to_list`

**Add Contact to List** · write, idempotent

Add an existing contact (by contact ID) to a list. Adding a contact that is already on the list has no effect. To add someone by email who may not exist yet, use directiq_upsert_contact.

| Argument | Type | Required | Description |
|---|---|---|---|
| `listId` | integer | yes | Numeric list ID (from directiq_list_lists) |
| `contactId` | integer | yes | Numeric contact ID (from directiq_list_contacts or directiq_get_contact) |

## `directiq_remove_contact_from_list`

**Remove Contact from List** · **destructive**, idempotent

Remove a contact from one list. The contact stays in the account and on its other lists; to delete the contact entirely, use directiq_delete_contact.

| Argument | Type | Required | Description |
|---|---|---|---|
| `listId` | integer | yes | Numeric list ID (from directiq_list_lists) |
| `contactId` | integer | yes | Numeric contact ID (from directiq_list_contacts or directiq_get_contact) |

## `directiq_list_segments`

**List Segments** · read-only

List saved segments (rule-based groups of contacts) with their IDs and names. Returns an empty array if the account has none. Segments are managed in the DirectIQ dashboard.

No arguments.

## `directiq_list_tags`

**List Tags** · read-only

List every tag in the account with its ID and name. Use to check whether a tag exists or to find its ID before renaming or deleting it.

No arguments.

## `directiq_get_tag`

**Get Tag** · read-only

Get one tag by ID with its name and details. Use directiq_list_tags to find the ID. Returns an error if no tag has that ID.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric tag ID (from directiq_list_tags) |

## `directiq_create_tag`

**Create Tag** · write

Create a new tag and return it with its ID. Check directiq_list_tags first to avoid duplicates. To tag a contact, use directiq_tag_contact, which also creates missing tags.

| Argument | Type | Required | Description |
|---|---|---|---|
| `name` | string | yes | Tag name |

## `directiq_rename_tag`

**Rename Tag** · **destructive**, idempotent

Rename a tag by ID. Contacts keep the tag under its new name; the previous name is overwritten.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric tag ID (from directiq_list_tags) |
| `name` | string | yes | New tag name |

## `directiq_delete_tag`

**Delete Tag** · **destructive**, idempotent

Permanently delete a tag by ID and remove it from every contact. Contacts are kept. This cannot be undone; confirm with the user first.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric tag ID (from directiq_list_tags) |

## `directiq_tag_contact`

**Tag Contact** · write, idempotent

Apply one or more tags to a contact by contact ID. Tags that do not exist yet are created. Applying a tag the contact already has has no effect.

| Argument | Type | Required | Description |
|---|---|---|---|
| `contactId` | integer | yes | Numeric contact ID (from directiq_list_contacts or directiq_get_contact) |
| `tags` | array of string | yes | Tag names to apply |

## `directiq_untag_contact`

**Untag Contact** · **destructive**, idempotent

Remove a tag from a contact, identified by the contact email and the tag name. The tag itself and the contact are kept; to delete the tag everywhere, use directiq_delete_tag.

| Argument | Type | Required | Description |
|---|---|---|---|
| `email` | string | yes | Contact email address |
| `tag` | string | yes | Tag name to remove |

## `directiq_list_campaigns`

**List Campaigns** · read-only

List email campaigns and automation flows with ID, name, type (Regular or Flow), status, subject, sender, target lists, recipient count, and scheduled send time. Returns { campaigns, nextPageToken }; pass nextPageToken back to get the next page (null means no more). Use the IDs with directiq_campaign_stats for performance.

| Argument | Type | Required | Description |
|---|---|---|---|
| `nextPageToken` | string | no | Token from the previous page; omit for the first page |

## `directiq_get_campaign`

**Get Campaign** · read-only

Get one campaign by ID: name, type, status, subject, sender, target lists and segments, template ID, recipient count, and scheduled send time. For opens, clicks, and other results, use directiq_campaign_stats.

| Argument | Type | Required | Description |
|---|---|---|---|
| `id` | integer | yes | Numeric campaign ID (from directiq_list_campaigns) |

## `directiq_campaign_stats`

**Campaign Stats** · read-only

Summarize how a campaign performed: counts of recipients, opens, clicks, bounces, unsubscribes, and complaints, plus open, click, bounce, and unsubscribe rates as percentages of recipients. Use directiq_campaign_report to see which contacts did what. A section that fails to load is returned as an error without failing the rest.

| Argument | Type | Required | Description |
|---|---|---|---|
| `campaignId` | integer | yes | Numeric campaign ID (from directiq_list_campaigns) |

## `directiq_campaign_report`

**Campaign Report** · read-only

List the contacts behind one campaign result, one page at a time: who received, opened, clicked, bounced, unsubscribed, or complained. Returns { totalCount, result }. Optionally filter to one email address, or for clicks, to one link. For totals and rates only, use directiq_campaign_stats.

| Argument | Type | Required | Description |
|---|---|---|---|
| `campaignId` | integer | yes | Numeric campaign ID (from directiq_list_campaigns) |
| `type` | string: `recipients`, `opens`, `clicks`, `bounces`, `unsubscribes`, `complaints` | yes | Which result to list |
| `email` | string | no | Only return rows for this contact email |
| `link` | string | no | For type "clicks" only: only return clicks on this URL |
| `pageNumber` | integer | no | 1-based page number (default 1) |
| `pageSize` | integer (max 500) | no | Results per page, up to 500 |

## `directiq_send_email`

**Send Transactional Email** · write, open-world

Send one transactional email (a receipt, confirmation, or one-to-one message) immediately. It is delivered to real recipients and cannot be recalled, and it counts toward the account’s sending plan. The from address must be a confirmed sender on the account (see directiq_account_overview). Not for bulk marketing: campaigns are sent from the DirectIQ dashboard.

| Argument | Type | Required | Description |
|---|---|---|---|
| `from` | string | yes | Sender address; must be a confirmed sender on the account |
| `to` | string | yes | Recipient email address |
| `subject` | string | yes | Subject line, at least 2 characters |
| `content` | string | yes | Message body as HTML or plain text, at least 2 characters |
| `fromName` | string | no | Sender display name, e.g. "Acme Support" |
| `cc` | array of string | no | CC recipient addresses |
| `bcc` | array of string | no | BCC recipient addresses |

## `directiq_list_endpoints`

**List DirectIQ Endpoints** · read-only

List known DirectIQ API operations (HTTP method, API group, path, summary). Optionally filter by a search term. Call GET operations with directiq_read_request and POST/PUT/PATCH/DELETE operations with directiq_write_request.

| Argument | Type | Required | Description |
|---|---|---|---|
| `search` | string | no | Optional case-insensitive filter matched against each operation path or summary |

## `directiq_read_request`

**DirectIQ Read Request** · read-only

Issue an authenticated GET request to any DirectIQ endpoint and return the JSON response. Prefer a dedicated directiq_* tool when one exists; use this for read operations without one, found with directiq_list_endpoints (e.g. segments for a contact, email client reports). Full request/response schemas for each group are in the DirectIQ API docs at https://rest.directiq.com/{group}/swagger/index.html (core, contacts, subscription).

| Argument | Type | Required | Description |
|---|---|---|---|
| `group` | string: `core`, `contacts`, `subscription` | yes | DirectIQ API group: core, contacts, or subscription |
| `path` | string | yes | Endpoint path within the group, e.g. '/contact/list' or '/email/send' |
| `query` | object | no | Optional query parameters |

## `directiq_write_request`

**DirectIQ Write Request** · **destructive**

Issue an authenticated POST, PUT, PATCH, or DELETE request to any DirectIQ endpoint. May create, modify, or permanently delete account data, including bulk operations. Prefer a dedicated directiq_* tool when one exists; use this only for operations without one, found with directiq_list_endpoints, and confirm destructive calls with the user first. Full request/response schemas for each group are in the DirectIQ API docs at https://rest.directiq.com/{group}/swagger/index.html (core, contacts, subscription).

| Argument | Type | Required | Description |
|---|---|---|---|
| `group` | string: `core`, `contacts`, `subscription` | yes | DirectIQ API group: core, contacts, or subscription |
| `method` | string: `POST`, `PUT`, `PATCH`, `DELETE` | yes | HTTP method |
| `path` | string | yes | Endpoint path within the group, e.g. '/contact/list' or '/email/send' |
| `query` | object | no | Optional query parameters |
| `body` | object or array | no | Optional JSON request body: an object, or an array for bulk operations |

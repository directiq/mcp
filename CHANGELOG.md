# Changelog

All notable changes to the DirectIQ MCP server (`https://mcp.directiq.com/mcp`) are documented here. This project follows [Semantic Versioning](https://semver.org).

## [0.2.0] - 2026-10-29
### Added
- Campaign tools: `directiq_list_campaigns`, `directiq_get_campaign`, `directiq_campaign_stats` (recipients, opens, clicks, bounces, unsubscribes, complaints, and rates), and `directiq_campaign_report` (the contacts behind each result).
- Contact tools: `directiq_upsert_contact` (create or update a contact on a list) and `directiq_delete_contact`.
- List tools: `directiq_rename_list`, `directiq_delete_list`, `directiq_add_contact_to_list`, `directiq_remove_contact_from_list`, and `directiq_list_segments`.
- Tag tools: `directiq_rename_tag`, `directiq_delete_tag`, `directiq_tag_contact`, and `directiq_untag_contact`.
- Server card at `/.well-known/mcp/server-card.json`.

### Changed
- Every tool description now says what the tool returns, when to use it instead of related tools, and its side effects.
- A `tools/call` without an API key now returns HTTP 401 with `WWW-Authenticate: ApiKey header="x-api-key"`. `initialize` and `tools/list` still work without a key.
- Upgraded to zod 4 and the latest MCP SDK; resolved all known dependency vulnerabilities.

### Fixed
- `directiq_list_contacts` always failed because it sent `page` instead of `pageNumber`. It now takes `pageNumber` and `pageSize` (50–500, default 50).

## [0.1.0] - 2026-10-12
### Added
- Hosted remote MCP server over Streamable HTTP at `https://mcp.directiq.com/mcp` (MCP protocol 2025-06-18).
- API-key authentication through the `x-api-key` header.
- 15 tools with MCP annotations: account overview; contact count, list, get, and update; list list, get, and create; tag list, get, and create; transactional email send; API endpoint discovery; generic read and write requests.

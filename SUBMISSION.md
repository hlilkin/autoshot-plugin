# Submission notes

Notes for the owner when listing this plugin. Nothing in this file is a secret. Last reviewed 19 November 2026.

## xAI plugin form (Grok Bot and Cursor)

Form: [x.ai/bot/marketplace/submit-a-plugin](https://x.ai/bot/marketplace/submit-a-plugin). It distributes a plugin or MCP server to Grok Bot and Cursor users after review. Merge this change first so the logo URL below serves the new logo.

| Form field | Value |
| --- | --- |
| First Name / Last Name | Hristo / Lilkin |
| Work Email | hristo@autoshotapp.com |
| Organization Name | AutoShot |
| Organization Handle (lowercase, no spaces, 40 characters max) | `autoshot` |
| Website URL (optional) | https://autoshotapp.com |
| Logotype URL (1:1 PNG, opaque plate) | https://raw.githubusercontent.com/hlilkin/autoshot-plugin/main/assets/logo.png |
| Where do you want to be listed? | Grok Bot and Cursor |
| MCP Server URL or Public GitHub Repository | https://github.com/hlilkin/autoshot-plugin (the full package: skills, rule, `mcp.json`). The MCP URL alone is `https://autoshotapp.com/api/mcp`. |
| Brief Description | Turn dealership lot photos into listing-ready studio images on the connected AutoShot account. One credit per new photo. |

Testing instructions (479 characters, limit 600):

```text
Sign up free at autoshotapp.com: 10 free photos, no card. In Grok Bot send "Add this MCP server: https://autoshotapp.com/api/mcp", tap Authorize, sign in with that account and approve the 3 permissions. Attach a car photo, ask "Process this with the showroom background", then reply Yes to the 1-credit cost. The bot uploads, processes, and shows the studio result. Cursor: add the URL to mcp.json, or clone the repo to ~/.cursor/plugins/local/autoshot and sign in when prompted.
```

Before ticking "tested locally and in-app", run a fresh sign-in, upload, and processing test on the final commit. That spends one credit.

## Cursor Marketplace

The Cursor Marketplace team replied to AutoShot's submission on 19 November 2026 that new submissions are limited for now, and suggested cursor.directory. The xAI form above is the route this file documents. [cursor.directory/plugins/new](https://cursor.directory/plugins/new) is a separate, optional community listing, and [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) is the Cursor Marketplace form.

Package checks (sources: [Plugins](https://cursor.com/docs/plugins), [reference](https://cursor.com/docs/reference/plugins), [manifest schema](https://raw.githubusercontent.com/cursor/plugins/main/schemas/plugin.schema.json)):

- [x] The repo is public and MIT-licensed.
- [x] `.cursor-plugin/plugin.json` is valid. `name` is `autoshot` and `repository` is this repo.
- [x] Skills and the rule have frontmatter. Manifest paths are relative.
- [x] `assets/logo.png` is a 1024×1024 PNG on an opaque plate.
- [x] `mcp.json` is URL-only: no `${VAR}` placeholders, no client secret, no headers. The server advertises `registration_endpoint`, so no static `auth` block is needed.
- [x] Validated with Cursor's `scripts/validate-plugins.mjs` ([cursor/plugins](https://github.com/cursor/plugins)) and `scripts/validate-template.mjs` ([cursor/plugin-template](https://github.com/cursor/plugin-template)) as of 1.2.0. Re-run after later changes.
- [ ] Check whether the `autoshot` name is available.

## Server facts

Checked against production on 19 November 2026.

- Protected resource: `https://autoshotapp.com/api/mcp`.
- Authorization metadata (`/.well-known/oauth-authorization-server/plugin/oauth`) advertises `registration_endpoint`, PKCE `S256` only, token auth `none`, and scopes `photos:read`, `photos:process`, `preferences:write`.
- An unauthenticated request to `/api/mcp` returns `401` with `scope="photos:read photos:process preferences:write"`.
- Cloud redirect `https://www.cursor.com/agents/mcp/oauth/callback`. Desktop redirect `http://localhost:8787/callback`.
- Privacy policy and terms (live) cover Cursor, Grok Bot, grok.com, and other MCP clients.

## Listing copy

| Field | Copy |
| --- | --- |
| Name | AutoShot |
| Plugin id | `autoshot` |
| Homepage | https://autoshotapp.com |
| Category | `integrations` |
| Keywords | automotive, dealership, photos, background-removal |

Long description:

AutoShot prepares vehicle photos for a dealer listing. It replaces the background with one of five studio styles, can place a logo already saved on the account onto exterior photos, and can blur a plate or apply a logo plate when a plate or holder is detected.

Connect an AutoShot account, confirm the background and plate treatment, and the assistant uploads the photo and starts processing. A new photo uses one existing credit. Failed jobs are refunded. The same photo and settings do not charge again. Buy credits on autoshotapp.com. The plugin cannot take payment.

Plate treatment does not guarantee that a plate is unreadable. The edit is meant to leave the vehicle itself unchanged, but results can vary, so compare each result with the original before publishing. AutoShot does not delete photo history.

## Keep out of this repo

Service-role keys, worker secrets, `.env` files, and reviewer prompts.

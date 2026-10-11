# Submission checklist

The operator completes this. Nothing in this file is a secret. Checked 10 October 2026.

Two catalogs are easy to mix up. The plugin listing is the Cursor Marketplace. Grok Bot's in-app Marketplace is where a person installs connectors, and no separate publisher form for that catalog was found. [x.ai/bot/marketplace](https://x.ai/bot/marketplace) lists Bot templates, not this plugin. [xai-org/plugin-marketplace](https://github.com/xai-org/plugin-marketplace) is Grok Build, a different product.

`Verified` means the linked page said it on that date. `Unverified` means it was not on the pages fetched, or a live login was not repeated from this repo.

## Cursor Marketplace

Submit the public repository at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

### Verified requirements

Sources: [Plugins](https://cursor.com/docs/plugins), [Plugins reference](https://cursor.com/docs/reference/plugins), [manifest schema](https://raw.githubusercontent.com/cursor/plugins/main/schemas/plugin.schema.json), [MCP](https://cursor.com/docs/mcp), [Marketplace security](https://cursor.com/help/security-and-privacy/marketplace-security).

- [ ] The repo is public. Marketplace plugins must be open source.
- [ ] `.cursor-plugin/plugin.json` is valid. `name` is `autoshot`: lowercase, unique, and kebab-case. The schema requires only `name`. `displayName`, `description`, `version`, `author`, `homepage`, `license`, `keywords`, and `logo` are optional and are set.
- [ ] `description` says what the plugin does. The manifest text is the short listing line below.
- [ ] Skills and the rule have the frontmatter those docs require. Paths in the manifest are relative and do not use `..`.
- [ ] `assets/logo.png` is committed and `logo` points at it. Relative logos are served from the repository.
- [ ] `README.md` explains install, the URL-only connection, credits, and what the plugin refuses to do.
- [ ] `mcp.json` has no `${VAR}` placeholders, so the manifest does not need a `variables` schema. It also has no `CLIENT_SECRET` and no headers.
- [ ] This is one plugin. Do not add `.cursor-plugin/marketplace.json` unless the repo later holds several plugins.
- [ ] Test locally: copy the folder to `~/.cursor/plugins/local/autoshot` and reload Cursor. Teams and Enterprise can disable local plugin imports.
- [x] Validated with Cursor's CI script `scripts/validate-plugins.mjs` from [github.com/cursor/plugins](https://github.com/cursor/plugins) and `scripts/validate-template.mjs` from [github.com/cursor/plugin-template](https://github.com/cursor/plugin-template). Both pass.
- [ ] Submit the repository link. Cursor reviews the listing and every later update before it ships. A plugin that is not maintained after a security report can be removed. Report plugin issues to security-reports@cursor.com.

### Unverified for Cursor

- [ ] The publish page title is "Publish a Cursor Marketplace Plugin" and the meta description calls it a plugin publisher application. The form fields beyond the repository link were not readable from the static HTML. Confirm the form while signed in. It is not the Origin app form at [cursor.com/docs/origin/apps/publish](https://cursor.com/docs/origin/apps/publish), which asks for a demo video. That video requirement was not in the plugin checklist.
- [ ] Screenshots are not in the plugin checklist. Prepare the set in "Listing copy" only if the form asks.
- [x] Category: the schema takes a free-text `category`. Every hosted-MCP connector in [cursor/plugins](https://github.com/cursor/plugins/tree/main/third_party) uses `integrations` (a few use `productivity`), so the manifest sets `integrations`.
- [ ] Confirm `autoshot` is free. A web search did not find a Cursor Marketplace plugin with that name. The catalog was not fully enumerated.
- [x] `repository` in `.cursor-plugin/plugin.json` is `https://github.com/hlilkin/autoshot-plugin`.
- [ ] A desktop Cursor login was not repeated for this package. Confirm both redirects and that the authorize URL includes `resource=https://autoshotapp.com/api/mcp`.

### Cursor install fact, verified

[MCP static OAuth](https://cursor.com/docs/mcp) says to put `auth.CLIENT_ID` in `mcp.json` when the provider does not support dynamic client registration. `CLIENT_ID` is required only inside that `auth` object. This server's metadata includes `registration_endpoint` `https://autoshotapp.com/plugin/oauth/register`, so the published `mcp.json` is the URL alone.

The public static client `54181693-917f-49f1-ad07-f40b596c511d` stays available as a local fallback if a Cursor build skips registration. Do not put it in the published file.

## Grok Bot plugin catalog

### What the docs actually describe

Verified, [Use the computer and apps](https://docs.x.ai/grok-bot/computer-and-apps): connectors are installed as plugins from Marketplace. The user opens Marketplace, chooses Add, finishes browser authentication, and can attach the connector with `@`. Installed connectors are account-wide.

Verified, [Settings and notifications](https://docs.x.ai/grok-bot/settings-and-notifications): plugins are not a settings section. Marketplace lists plugins and packaged skills. Your plugins shows what is installed. Team and Enterprise admins can require or restrict plugins.

Verified, [Connect plugins](https://cursor.com/help/grok-bot/connect-plugins): sidebar Plugins, or the mobile avatar, then Authorize in the browser. Added is not the same as signed in. Waiting for authorization has a Reopen action. A teammate's Team Bot asks before it uses the connected account. Disabled by team admin is a Cursor team control.

Verified, [Grok Bot for teams](https://cursor.com/docs/grok-bot/teams): Grok Bot inherits the team's Cursor MCP policy. Connectors appear as plugins. There is no separate Grok Bot connector list for admins. OAuth tokens stay on Cursor's connector backend, and bots call tools without receiving the token. Pushing a connector as mandatory or default-on is not available.

Verified, [cursor.com/docs/mcp](https://cursor.com/docs/mcp): Cursor's documented redirects are `https://www.cursor.com/agents/mcp/oauth/callback` (web and Cursor Agents) and `http://localhost:8787/callback` (desktop). A Cursor staff forum post names the same cloud callback for Grok Bot's connect card.

### Checklist for a Grok Bot user-facing listing

- [ ] Publish through the Cursor Marketplace steps above. No Grok Bot publisher URL was found on the docs pages above.
- [ ] After Cursor lists it, install from Grok Bot's Marketplace and complete Authorize. Confirm the browser lands on AutoShot and a photo can be uploaded and processed.
- [ ] Custom install, before or without a listing, is the URL only: `Add this MCP server: https://autoshotapp.com/api/mcp`. Do not tell the user to paste a client id. A Grok Bot end-to-end test of sign-in, upload, and processing already passed on the operator's side. This repo did not repeat that test.
- [ ] On a team, ask the admin to allow `https://autoshotapp.com/api/mcp` if the plugin shows Disabled by team admin.

### Unverified for Grok Bot

- [ ] Whether Cursor Marketplace approval automatically shows AutoShot in every Grok Bot user's Marketplace. The docs describe install and team policy. They do not say the public Cursor catalog is copied into Grok Bot one for one. Confirm after approval.
- [ ] A Grok Bot screenshot, icon size, or category requirement for plugin publishers. None was stated on the pages above.
- [ ] The exact grok.com redirect URI. The operator says registration allows grok.com callbacks in addition to the Cursor and Grok Bot callbacks. The metadata document does not list redirect URIs, so this repo cannot confirm the string.

## x.ai/bot/marketplace is Bot templates

Verified by opening [x.ai/bot/marketplace](https://x.ai/bot/marketplace) on 10 October 2026. The page is titled Grok Bot Marketplace and lists Bot templates with Add. Headings on that page: Featured, From Grok Bot Team, Engineering, Sales, Marketing, Design, Personal, Recruiting & People, Product, Operations.

Verified, [Create and manage Bots](https://docs.x.ai/grok-bot/bots): Marketplace in the sidebar adds a Bot by copying its template. Sharing is Create template from the Bot's Share menu, then a public link or a team-only link. The recipient previews it on x.ai and chooses Add to Grok Bot. A shared Bot does not include the creator's computer, logins, or conversation history. Shared Bots are third-party and accept third-party bot terms.

Do not submit this Git repository there as a plugin. A dealership Bot template is a separate optional product. If the operator wants one later:

- [ ] Create the Bot in Grok Bot, with instructions to use the AutoShot plugin and to confirm before spending a credit.
- [ ] Remove API keys, internal URLs, and customer data before sharing.
- [ ] Create template, choose Public link or Team-only, and send the link.
- [ ] Unverified: a form that puts a template into Featured or a category on x.ai/bot/marketplace. The docs describe a share link, not an editorial submission.

## Do not file this as a Grok Build plugin

Verified, [xAI Plugin Marketplace README](https://github.com/xai-org/plugin-marketplace/blob/main/README.md) and [CONTRIBUTING.md](https://github.com/xai-org/plugin-marketplace/blob/main/CONTRIBUTING.md): that repository is the catalog for Grok Build. A third party opens a pull request, adds one entry to `.grok-plugin/marketplace.json`, pins a full 40-character commit SHA for a remote source, regenerates `.grok-plugin/plugin-index.json`, and passes `validate-catalog.py`. Grok Build reads `.mcp.json`, not this repo's `mcp.json`.

Leave that pull request unopened unless the operator separately decides to list on Grok Build. The Cursor package should not be rewritten to fit it for this submission.

## grok.com custom connector

Verified, [Connectors](https://docs.x.ai/grok/connectors): a custom MCP connector is added at grok.com/connectors with New Connector, then Custom, then the MCP server URL and whatever authentication the server requires. The server must be reachable on the public internet. That page does not say how a vendor is added to the preconfigured catalog.

- [ ] Users who are not in Grok Bot can connect with the URL `https://autoshotapp.com/api/mcp` only.
- [ ] Unverified: a publisher application for the preconfigured catalog at grok.com/connectors. None was found.

## Server facts already true

Verified from production on 10 October 2026 unless noted.

- [x] Metadata at `https://autoshotapp.com/.well-known/oauth-authorization-server/plugin/oauth` includes `registration_endpoint` `https://autoshotapp.com/plugin/oauth/register`, PKCE `S256`, token auth `none`, and scopes `photos:read`, `photos:process`, `preferences:write`.
- [x] Protected resource is `https://autoshotapp.com/api/mcp`.
- [x] Unauthenticated `POST /api/mcp` returns `401` with `scope="photos:read photos:process preferences:write"`.
- [x] `OPTIONS` on the registration URL returns `204` and `Allow: OPTIONS, POST`. A client was not registered from this repo.
- [ ] Operator: `create_photo_upload` is live, and a Grok Bot test completed sign-in, upload, and processing. Keep the ChatGPT file path working for the ChatGPT client.
- [ ] Operator: consent and the account connections list name Cursor, Grok Bot, and grok.com from the client that started the request, not as ChatGPT.
- [ ] Operator: decide whether plugin image starts share the existing OpenAI start ceiling. That setting is not in this repo.

## This repository

- [x] Public Git repository: [github.com/hlilkin/autoshot-plugin](https://github.com/hlilkin/autoshot-plugin).
- [x] `repository` in `.cursor-plugin/plugin.json` is that URL.
- [ ] Confirm the logo. `assets/logo.png` is a 512×512 render of [autoshotapp.com/icon.svg](https://autoshotapp.com/icon.svg), not a byte copy of the private app icon.
- [ ] Read `mcp.json` and confirm it contains only the production URL.
- [ ] Keep the staging client id in the README dev section only.
- [ ] Do not add service-role keys, worker secrets, `.env` files, or reviewer prompts.

## Listing copy

Use this for the Cursor Marketplace form and for the Grok Bot plugin card if that catalog asks for the same text. It is written for dealerships.

### Cursor Marketplace and Grok Bot plugin

| Field | Copy |
| --- | --- |
| Name | AutoShot |
| Plugin id | `autoshot` |
| Homepage | https://autoshotapp.com |
| Short description | Turn dealership lot photos into listing-ready studio images on the connected AutoShot account. One credit per new photo. |
| Category | `integrations`, the value Cursor uses for hosted-MCP connectors. If the form offers a different control, pick the closest match. |
| Keywords | automotive, dealership, photos, background-removal |

Long description:

AutoShot prepares vehicle photos for a dealer listing. It replaces the background with one of five studio styles, can place a logo already saved on the account onto exterior photos, and can blur a plate or apply a logo plate when a plate or holder is detected.

Connect an AutoShot account, confirm the background and plate treatment, and the assistant uploads the photo and starts processing. A new photo uses one existing credit. Failed jobs are refunded. The same photo and settings do not charge again. Buy credits on autoshotapp.com. The plugin cannot take payment.

Plate treatment does not guarantee that a plate is unreadable. Compare each result with the original before publishing. AutoShot does not change paint or body style, and it does not delete photo history.

Screenshots to prepare if a form asks. Not required by the Cursor checklist that was verified:

1. A lot photo beside the studio result, using a vehicle the dealer has the right to show. No customer documents.
2. The consent screen showing AutoShot and the three scopes `photos:read`, `photos:process`, and `preferences:write`. Crop out cookies and tokens.
3. A chat that shows the credit confirmation and the finished preview, with no signed download URL visible.

### Optional Bot template on x.ai/bot/marketplace

Only if the operator also publishes a Bot. This is not the plugin listing.

| Field | Copy |
| --- | --- |
| Name | Lot Photo Desk |
| Closest heading seen on the page | Operations. Unverified whether a shared template can be filed under a heading. |
| Card description | Turns a dealership's lot photos into studio listing images with AutoShot. Confirms the background and the one-credit cost before each photo, and never buys credits or publishes a result on its own. |

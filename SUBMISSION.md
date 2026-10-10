# Marketplace submission checklist

The owner completes this. Nothing here is a secret. The production client id is a public OAuth client.

Cursor reviews every listing and every later update. Plugins must stay in a public Git repository. Review covers this package. It does not require publishing the AutoShot application.

Submit at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).

The plugins reference says to submit the repository link. The publish page title is "Publish a Cursor Marketplace Plugin" and its meta description calls the form a plugin publisher application. The form fields beyond that were not readable from the static HTML on 10 October 2026.

## Before this package is honest

These changes live in the private AutoShot app, not in this repo.

- [ ] The production client `54181693-917f-49f1-ad07-f40b596c511d` is a public client for resource `https://autoshotapp.com/api/mcp`. PKCE is `S256`. Token endpoint auth is `none`. There is no client secret.
- [ ] That client allows only these redirects:
  - `https://www.cursor.com/agents/mcp/oauth/callback`
  - `http://localhost:8787/callback`
- [ ] Dynamic client registration stays off. There is no `registration_endpoint`.
- [ ] `create_photo_upload` is deployed. It returns a short-lived signed PUT under the signed-in user's prefix and does not spend a credit.
- [ ] `start_photo_processing` accepts that upload, reserves one credit, refunds on failure, and reuses the same file and settings without a second charge. The ChatGPT file path still works for the ChatGPT client.
- [ ] The ChatGPT client, its redirect, and its metadata document are unchanged.
- [ ] Privacy and terms, in every locale, name Cursor and Grok Bot: the grant record, that Cursor stores the OAuth token, that AutoShot does not receive the transcript, that the photo still goes to OpenAI's image API, and that disconnect stops later calls. Link [cursor.com/privacy](https://cursor.com/privacy).
- [ ] The consent screen and the account connections list name this client as Cursor or Grok Bot, not as ChatGPT.
- [ ] A real Cursor session has shown whether the result card renders. The skill already tells the agent to fall back to preview images and Recent photos on the website when no card appears. Change the skill if a test shows something else.
- [ ] Decide whether plugin image starts share the project's existing OpenAI start ceiling or get a lower one. That ceiling is not configured in this repo.

## This repository

- [ ] Create a public Git repository and push this branch. Marketplace plugins have to be open source. This environment does not publish a public GitHub URL by itself.
- [ ] Set `repository` in `.cursor-plugin/plugin.json` to that public URL before submitting. The field is optional in the schema and is omitted until the public URL exists.
- [ ] Search the marketplace and confirm the name `autoshot` is free. A web search on 10 October 2026 did not find a Cursor Marketplace plugin with that name. The full catalog was not enumerated. If the name is taken, pick another lowercase kebab-case name and change it in `.cursor-plugin/plugin.json` only.
- [ ] Confirm `assets/logo.png` is the mark you want reviewers to see. It was rendered from the public `https://autoshotapp.com/icon.svg` hexagon. The private app's `icon-512.png` was not in this workspace.
- [ ] Read `mcp.json` and confirm the only client id is `54181693-917f-49f1-ad07-f40b596c511d`, the URL is production, and there is no `CLIENT_SECRET`, header, or `${VAR}`.
- [ ] Keep the staging client id in the README dev section only. Do not copy it into `mcp.json`.
- [ ] Do not add service-role keys, worker secrets, `.env` files, reviewer prompts, or fixture URLs.

## Local test

- [ ] Copy the folder to `~/.cursor/plugins/local/autoshot`.
- [ ] Reload Cursor. On Teams or Enterprise, confirm an admin allows local plugin imports.
- [ ] Customize shows both skills, the credit rule, and the AutoShot MCP server.
- [ ] Authorize from the desktop app (`http://localhost:8787/callback`) and once from the web or a Cloud Agent (`https://www.cursor.com/agents/mcp/oauth/callback`).
- [ ] Confirm the authorize URL includes `resource=https://autoshotapp.com/api/mcp`.
- [ ] Process one real photo: one credit is reserved, an identical second start is reused, a failure refunds, three in-flight jobs return `busy`, and a zero balance returns `insufficient_credits`.
- [ ] Disconnect on the account page and confirm the next tool call fails until the user connects again.

## Submit and follow up

- [ ] Open [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) and submit the public repository.
- [ ] After it is listed, install from Cursor Customize and from Grok Bot's Plugins sidebar. Authorize again and confirm the browser lands on AutoShot.
- [ ] For a custom Grok Bot install before the listing, use the chat text in the README. Include the static client id. A URL alone is not enough, because this server does not register clients dynamically.
- [ ] When the tool contract changes, update this repo and submit the update. Cursor reviews updates before they ship. Production incidents are handled in the private app, including with the existing plugin enable flags.

## Docs this checklist follows

Checked 10 October 2026:

- [Plugins](https://cursor.com/docs/plugins)
- [Plugins reference](https://cursor.com/docs/reference/plugins), including the submission checklist
- [Plugin manifest schema](https://raw.githubusercontent.com/cursor/plugins/main/schemas/plugin.schema.json)
- [MCP, static OAuth](https://cursor.com/docs/mcp)
- [Marketplace security](https://cursor.com/help/security-and-privacy/marketplace-security)
- [Connect plugins (Grok Bot)](https://cursor.com/help/grok-bot/connect-plugins)
- [Grok Bot for teams](https://cursor.com/docs/grok-bot/teams)
- [Agent Skills](https://cursor.com/docs/skills)

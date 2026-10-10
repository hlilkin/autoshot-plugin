# AutoShot for Cursor and Grok Bot

AutoShot ([autoshotapp.com](https://autoshotapp.com)) turns dealership lot photos into listing images with a consistent studio background. This repository is the public Cursor Marketplace plugin. It does not contain the AutoShot application.

The plugin connects Cursor and Grok Bot to the production MCP server at `https://autoshotapp.com/api/mcp` (Streamable HTTP). Sign-in uses OAuth with PKCE S256 and a public client. There is no client secret and no dynamic client registration.

The MIT license covers this package: the manifest, skills, rule, logo, and docs. The hosted service is governed by [AutoShot's terms](https://autoshotapp.com/terms) and [privacy policy](https://autoshotapp.com/privacy).

## What it does

- Replaces the background of a vehicle photo with one of five studio styles.
- Optionally stamps a logo already saved on the AutoShot account onto exterior photos.
- Optionally applies a heavy plate blur, or a logo plate, when a plate or holder is detected. That treatment is best-effort and does not guarantee that a plate is unreadable.
- Uses the signed-in account's existing credits. One new photo reserves one credit. A failed job refunds it. The same photo and settings reuse the earlier job and do not charge again.

## What it does not do

- Paint, body, wheel, or other vehicle edits. Compare each result with the original before publishing.
- Purchases, checkout, or credit packs. Buy credits on the website.
- Deleting history.
- A promise that plate anonymization succeeded.
- Reading the chat transcript. AutoShot receives tool arguments, including the uploaded photo.

## Install in Cursor

After the plugin is listed:

1. Open Customize in the sidebar.
2. Search for AutoShot and install it.
3. Choose Authorize and sign in to an existing AutoShot account, or create one at [autoshotapp.com](https://autoshotapp.com).
4. Approve `photos:read`, `photos:process`, and `preferences:write`.

Cursor asks before MCP tool calls by default. On a Teams or Enterprise account, an admin can block the server. A blocked plugin still installs, and the server cannot be called. Enterprise admins control that from the MCP allowlist.

To try the folder before it is listed, copy this repository to `~/.cursor/plugins/local/autoshot`, reload Cursor, and confirm the skills, the rule, and the MCP server under Customize. On Teams and Enterprise, local plugin imports may be disabled by an admin.

## Connect

The production manifest uses a static OAuth client so Cursor does not try dynamic registration.

| Piece | Value |
| --- | --- |
| MCP endpoint | `https://autoshotapp.com/api/mcp` |
| Resource | `https://autoshotapp.com/api/mcp` (must match exactly) |
| Issuer | `https://autoshotapp.com/plugin/oauth` |
| Metadata | `https://autoshotapp.com/.well-known/oauth-authorization-server/plugin/oauth` |
| Client id | `54181693-917f-49f1-ad07-f40b596c511d` |
| Client secret | None. The token endpoint advertises `none`. |
| PKCE | `S256` only |
| Scopes | `photos:read`, `photos:process`, `preferences:write` |
| Web, Cloud Agents, and Grok Bot redirect | `https://www.cursor.com/agents/mcp/oauth/callback` |
| Desktop and CLI redirect | `http://localhost:8787/callback` |

The client id is public on purpose. Do not add a client secret to `mcp.json`.

## Credits

Processing uses the same credit ledger as the website. One new photo reserves one credit before the model runs. Failed jobs are refunded. A zero balance returns `insufficient_credits` and does not start a job. At most three plugin jobs may be queued or processing on an account.

The plugin cannot buy credits. Buy a pack at [autoshotapp.com](https://autoshotapp.com). New accounts receive the same signup credits as the website.

## Disconnect

Revoke the connection on the AutoShot account page: [autoshotapp.com/account](https://autoshotapp.com/account). That stops later tool calls. Grants last up to 90 days unless revoked sooner. Cursor keeps the OAuth token on its connector backend. The bot calls tools without receiving the token.

## Data

The photo is uploaded to AutoShot and then sent to OpenAI's image API for the edit. Processed results are kept for seven days, the same as the website. Signed download links last one hour. AutoShot does not receive the conversation text.

The service is for people 18 or older, matching [the terms](https://autoshotapp.com/terms).

## Tools

Read tools do not spend a credit: `get_autoshot_guide`, `list_studio_backgrounds`, `get_photo_preferences`, `list_recent_photos`, `get_photo_processing_status`, and `show_photo_result`.

`save_photo_preferences` needs `preferences:write`. It spends no credit and does not process a photo. Call it only when the user asks to save defaults.

`create_photo_upload` returns a short-lived signed PUT into the signed-in user's folder. It does not spend a credit. The agent uploads the photo there. Source photos can be up to 25 MiB. Image bytes do not go in the tool call.

`start_photo_processing` accepts that upload, reserves one credit, and refunds the credit if the job fails. Do not pass a model-chosen download URL.

Background ids: `showroom`, `light-tunnel`, `gallery-spotlight`, `slat-wall`, `shadow-grey`.

## Grok Bot

Grok Bot installs Cursor Marketplace plugins. OAuth tokens stay on Cursor's connector backend. A plugin connection belongs to the Grok Bot account that authorized it, and an installed plugin is available to bots on that account. A teammate's Team Bot asks before it uses the connected account. If the plugin shows Disabled by team admin, a Cursor team admin has to allow the server. Grok Bot uses the team's Cursor MCP policy. There is no separate Grok Bot connector list.

### From the marketplace

1. In Grok Bot, open Plugins on the sidebar. In the mobile app, tap the avatar at the top left and select Plugins.
2. Search for AutoShot and add it.
3. When Grok Bot asks you to Authorize, finish AutoShot sign-in in the browser and approve the three scopes.
4. If it says Waiting for authorization, use Reopen.
5. Confirm AutoShot is under Installed. Added means installed, not signed in. If it shows Needs auth or Disconnected, choose Authorize again.

### Custom MCP, before the listing

Grok Bot has no settings form for a custom server. Add it from the chat. The server must be public HTTPS. Localhost and stdio servers are not reachable from the bot.

Send:

```text
Add this MCP server.
Name: AutoShot
URL: https://autoshotapp.com/api/mcp
Static OAuth client id: 54181693-917f-49f1-ad07-f40b596c511d
Scopes: photos:read photos:process preferences:write
No client secret. Do not use dynamic client registration.
```

Then use Authorize and sign in to AutoShot. The cloud callback is `https://www.cursor.com/agents/mcp/oauth/callback`. The `resource` parameter must be `https://autoshotapp.com/api/mcp`.

Cursor's documented chat example is the URL alone. This server does not offer dynamic registration, so the static client id has to be stored with the server. If the connect card keeps only the URL and OAuth fails looking for a registration endpoint, add the server again and include the client id in that same message.

## Develop against staging

Staging is for a local copy of this plugin only. Do not put these values in `mcp.json` or in the marketplace manifest.

| Piece | Staging |
| --- | --- |
| MCP URL | `https://staging.autoshotapp.com/api/mcp` |
| Resource | `https://staging.autoshotapp.com/api/mcp` |
| Issuer | `https://staging.autoshotapp.com/plugin/oauth` |
| Metadata | `https://staging.autoshotapp.com/.well-known/oauth-authorization-server/plugin/oauth` |
| Public client id | `85195ac0-f860-45cf-91d2-92a71088cb1a` |

Same scopes, PKCE `S256`, no client secret, and no registration endpoint. Use the same two Cursor redirect URLs as production. Edit only a local `mcp.json` when pointing a test install at staging.

On 10 October 2026 the staging metadata document returned that issuer, those scopes, `token_endpoint_auth_methods_supported: ["none"]`, and `code_challenge_methods_supported: ["S256"]`. The protected-resource document named `https://staging.autoshotapp.com/api/mcp`. An unauthenticated `POST` to the staging MCP URL returned `401`. This repository cannot see whether the staging client id is registered.

## Layout

```text
.cursor-plugin/plugin.json
mcp.json
skills/process-vehicle-photo/SKILL.md
skills/autoshot-account/SKILL.md
rules/vehicle-photo-credits.mdc
assets/logo.png
README.md
LICENSE
PRIVACY.md
TERMS.md
SUBMISSION.md
```

`assets/logo.png` is a 512×512 render of the public mark at [autoshotapp.com/icon.svg](https://autoshotapp.com/icon.svg): an orange hexagon, `#ff6b2c`.

Submission steps for the owner are in [SUBMISSION.md](SUBMISSION.md).

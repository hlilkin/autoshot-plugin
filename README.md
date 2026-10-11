# AutoShot for Cursor and Grok Bot

AutoShot ([autoshotapp.com](https://autoshotapp.com)) turns dealership lot photos into listing images with a consistent studio background. [This repository](https://github.com/hlilkin/autoshot-plugin) is the public Cursor Marketplace plugin. It does not contain the AutoShot application.

The plugin connects Cursor and Grok Bot to the production MCP server at `https://autoshotapp.com/api/mcp` (Streamable HTTP). Install needs that URL only. Sign-in is OAuth with PKCE `S256` and [dynamic client registration](https://autoshotapp.com/plugin/oauth/register). There is no client secret.

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

To try the folder before it is listed, clone [github.com/hlilkin/autoshot-plugin](https://github.com/hlilkin/autoshot-plugin) into `~/.cursor/plugins/local/autoshot`, reload Cursor, and confirm the skills, the rule, and the MCP server under Customize. On Teams and Enterprise, local plugin imports may be disabled by an admin.

## Connect

`mcp.json` publishes the URL and nothing else. Cursor's MCP docs use an `auth` object only when the provider does not support dynamic client registration. `CLIENT_ID` is required inside that object, not on a URL-only server. This server advertises `registration_endpoint`, so the published file leaves `auth` out. Adding a static client id would select the static client instead of registration.

| Piece | Value |
| --- | --- |
| MCP endpoint | `https://autoshotapp.com/api/mcp` |
| Resource | `https://autoshotapp.com/api/mcp` (must match exactly) |
| Issuer | `https://autoshotapp.com/plugin/oauth` |
| Metadata | `https://autoshotapp.com/.well-known/oauth-authorization-server/plugin/oauth` |
| Registration | `https://autoshotapp.com/plugin/oauth/register` |
| Client secret | None. The token endpoint advertises `none`. |
| PKCE | `S256` only |
| Scopes | `photos:read`, `photos:process`, `preferences:write` |
| Web, Cloud Agents, and Grok Bot redirect | `https://www.cursor.com/agents/mcp/oauth/callback` |
| Desktop and CLI redirect | `http://localhost:8787/callback` |

An unauthenticated call returns `401` with `scope="photos:read photos:process preferences:write"`.

### Optional static client

Use this only if a Cursor build fails registration and you are editing a local `mcp.json`. Do not put it in the published file, and do not ask a Grok Bot user to paste it. There is still no client secret.

```json
"auth": {
  "CLIENT_ID": "54181693-917f-49f1-ad07-f40b596c511d",
  "scopes": ["photos:read", "photos:process", "preferences:write"]
}
```

## Credits

Processing uses the same credit ledger as the website. One new photo reserves one credit before the model runs. Failed jobs are refunded. A zero balance returns `insufficient_credits` and does not start a job. At most three plugin jobs may be queued or processing on an account.

The plugin cannot buy credits. Buy a pack at [autoshotapp.com](https://autoshotapp.com). New accounts receive the same signup credits as the website.

## Disconnect

Revoke the connection on the AutoShot account page: [autoshotapp.com/account](https://autoshotapp.com/account). That stops later tool calls. Grants last up to 90 days unless revoked sooner. Cursor keeps the OAuth token on its connector backend. The bot calls tools without receiving the token.

## Data

The photo is uploaded to AutoShot and then sent to OpenAI's image API for the edit. Processed results are kept for seven days, the same as the website. In hosts that cannot render a result card, such as Cursor and Grok Bot, `show_photo_result` returns preview images plus each photo's full-resolution signed download link. Those links last one hour, and anyone holding a valid link can open that result until it expires. AutoShot does not receive the conversation text.

The published terms require age 13 or older. Anyone under 18 needs permission from a parent or legal guardian. See [the terms](https://autoshotapp.com/terms). The [privacy policy](https://autoshotapp.com/privacy) and [terms](https://autoshotapp.com/terms) have sections for Cursor, Grok Bot, and grok.com.

## Tools

Read tools do not spend a credit: `get_autoshot_guide`, `list_studio_backgrounds`, `get_photo_preferences`, `list_recent_photos`, `get_photo_processing_status`, and `show_photo_result`.

`show_photo_result` shows the finished photos. Where no result card is available (Cursor and Grok Bot), it returns preview images and a text block with each photo's full-resolution signed download link, valid for one hour. The agent downloads the files from those links and hands them to the user, or shares the links.

`save_photo_preferences` needs `preferences:write`. It spends no credit and does not process a photo. Call it only when the user asks to save defaults.

`create_photo_upload` returns a short-lived signed PUT into the signed-in user's folder. It does not spend a credit. The agent uploads the photo there. Source photos can be up to 25 MiB. Image bytes do not go in the tool call.

`start_photo_processing` accepts that upload, reserves one credit, and refunds the credit if the job fails. Do not pass a model-chosen download URL.

Background ids: `showroom`, `light-tunnel`, `gallery-spotlight`, `slat-wall`, `shadow-grey`.

## Grok Bot

After AutoShot appears in Grok Bot's Marketplace, install it from Plugins. Until then, add the public MCP URL from chat. OAuth tokens for that Cursor-hosted connection stay on Cursor's connector backend. A plugin connection belongs to the Grok Bot account that authorized it, and an installed plugin is available to bots on that account. A teammate's Team Bot asks before it uses the connected account. On a Cursor team, Grok Bot uses that team's MCP policy. A blocked server shows as Disabled by team admin.

### From the marketplace

1. In Grok Bot, open Plugins on the sidebar. In the mobile app, tap the avatar at the top left and select Plugins.
2. Search for AutoShot and add it.
3. When Grok Bot asks you to Authorize, finish AutoShot sign-in in the browser and approve the three scopes.
4. If it says Waiting for authorization, use Reopen.
5. Confirm AutoShot is under Installed. Added means installed, not signed in. If it shows Needs auth or Disconnected, choose Authorize again.

### Custom MCP

Grok Bot has no settings form for a custom server. Add it from the chat. The server must be public HTTPS. Localhost and stdio servers are not reachable from the bot.

Send:

```text
Add this MCP server: https://autoshotapp.com/api/mcp
```

Then use Authorize and sign in to AutoShot. Registration supplies the client. Do not paste a client id or a client secret. The cloud callback is `https://www.cursor.com/agents/mcp/oauth/callback`. The `resource` parameter must be `https://autoshotapp.com/api/mcp`.

## Develop against staging

Staging is for a local copy of this plugin only. Do not put these values in `mcp.json` or in the marketplace manifest.

| Piece | Staging |
| --- | --- |
| MCP URL | `https://staging.autoshotapp.com/api/mcp` |
| Resource | `https://staging.autoshotapp.com/api/mcp` |
| Issuer | `https://staging.autoshotapp.com/plugin/oauth` |
| Metadata | `https://staging.autoshotapp.com/.well-known/oauth-authorization-server/plugin/oauth` |
| Registration | `https://staging.autoshotapp.com/plugin/oauth/register` |
| Optional static client id | `85195ac0-f860-45cf-91d2-92a71088cb1a` |

A staging test uses the same URL-only `mcp.json` shape, with the staging URL substituted in a local copy. Same scopes, PKCE `S256`, and no client secret. The static client id above is only for a local override if registration fails. Use the same two Cursor redirect URLs as production.

On 10 October 2026 the staging metadata document returned that issuer, `registration_endpoint`, those scopes, `token_endpoint_auth_methods_supported: ["none"]`, and `code_challenge_methods_supported: ["S256"]`. An unauthenticated `POST` returned `401` with `scope="photos:read photos:process preferences:write"`. This repository cannot see whether the staging static client id is still registered.

## Layout

```text
.cursor-plugin/plugin.json
mcp.json
skills/process-vehicle-photo/SKILL.md
skills/autoshot-account/SKILL.md
rules/vehicle-photo-credits.mdc
assets/logo.png
README.md
CHANGELOG.md
LICENSE
PRIVACY.md
TERMS.md
SUBMISSION.md
```

`assets/logo.png` is a 512×512 render of the public mark at [autoshotapp.com/icon.svg](https://autoshotapp.com/icon.svg): an orange hexagon, `#ff6b2c`.

Source: [github.com/hlilkin/autoshot-plugin](https://github.com/hlilkin/autoshot-plugin). Submission steps for the owner are in [SUBMISSION.md](SUBMISSION.md).

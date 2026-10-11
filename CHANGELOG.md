# Changelog

All notable changes to this plugin will be documented here.

## 1.2.0

- Set `"type": "http"` on the `autoshot` MCP server in `mcp.json`. The URL is unchanged (`https://autoshotapp.com/api/mcp`), with no auth block or headers.
- Added `category` (`integrations`) and `tags` to the plugin manifest.
- The credits rule (`rules/vehicle-photo-credits.mdc`) is no longer always-on. It keeps its description so the agent can pull it in when a vehicle photo job comes up.
- Result download guidance: in Cursor and Grok Bot, where no result card appears, the skill now has the agent download each full-resolution file from the signed links `show_photo_result` returns (valid for one hour) and hand them to the user, never editing a signed URL.
- Docs refresh: README links the live privacy policy and terms, which now cover Cursor, Grok Bot, and grok.com; the submission checklist records validation with Cursor's CI scripts.

## 1.1.0 — initial package

- Added the `autoshot` MCP server pointing at `https://autoshotapp.com/api/mcp`, signed in with OAuth (PKCE `S256`, dynamic client registration), no client secret.
- Added the `process-vehicle-photo` and `autoshot-account` skills and the credits rule.
- Logo: a render of the public AutoShot mark.

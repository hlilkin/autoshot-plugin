# Changelog

All notable changes to this plugin will be documented here.

## 1.2.1

- Logo: `assets/logo.png` is now a 1024×1024 PNG with the orange hexagon on an opaque dark plate (xAI's form asks for a 1:1 logo with a background plate). It was a 512×512 hexagon on transparent pixels.
- `PRIVACY.md` and `TERMS.md` now say the live policies cover Cursor, Grok Bot, grok.com, and other MCP clients. The "not covered" and "draft paragraphs" wording is gone.
- README: the Cursor install steps use the `mcp.json` or local plugin folder route and no longer imply a directory listing. The staging section is removed. Retention wording now notes that a result tied to an unresolved report is kept.
- `SUBMISSION.md` is rewritten and shorter. It adds the xAI plugin form fields (organization handle `autoshot`, MCP URL, repository, logo URL, description, testing instructions) and drops outdated catalog notes and a dead Grok help link.
- Skills: seven-day retention is qualified as "normally removed", and the age line mentions the higher local minimum.
- Also ships the README update from PR #2, which had no version bump: a Quick start with the Grok Bot connector URL and Cursor JSON snippet, three example prompts, a before/after demo image (`assets/demo-before-after.jpg`), and two fixed wording errors.

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

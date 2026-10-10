---
name: process-vehicle-photo
description: Prepare a vehicle photo for a listing with AutoShot studio background replacement. Use when the user wants a car, truck, or other vehicle photo processed, a background replaced, a plate blurred or branded, or a listing image made from a lot photo. Do not use for paint or body edits, credit purchases, history deletion, or photos that are not vehicles.
---

# Process a vehicle photo

AutoShot replaces the background of a vehicle photo with one of five studio styles. It can stamp a saved dealership logo on exteriors, and it can blur a plate or place a logo plate. The connected AutoShot account pays for processing. This skill is instructions only. The server enforces scopes, uploads, credits, and rate limits.

## Before spending a credit

1. Call `get_photo_preferences`. If the user asks what is available, also call `list_studio_backgrounds`.
2. Background ids are `showroom`, `light-tunnel`, `gallery-spotlight`, `slat-wall`, and `shadow-grey`. Use the titles the tool returns.
3. Tell the user the background, the plate treatment, whether a saved logo will stamp exterior photos, and the cost: one AutoShot credit per photo. Wait for an explicit yes before calling `start_photo_processing`.
4. Interiors keep the cabin and are not logo-stamped. Ask before spending a credit on a cabin photo.
5. Plate blur and logo plates are best-effort. They are not a promise that a plate is unreadable or that a holder was detected. The edit is meant to leave the vehicle itself alone. Tell the user to compare the result with the original before publishing.

## Upload, then process

Do not inline image bytes. The MCP request body cannot carry a photo. Do not pass a `download_url`, a public URL, or any URL you constructed or found. A model-supplied URL is not a photo the user attached.

1. If `create_photo_upload` is not available, stop. Tell the user this chat cannot send the photo yet. Do not invent another way to hand the server a URL.
2. Call `create_photo_upload` once per photo. It returns a short-lived signed PUT into that user's own folder. It does not spend a credit.
3. PUT the photo bytes to that signed URL. Source photos can be up to 25 MiB. Use the upload handle the tool returns. Do not reuse a URL from another account or an earlier run.
4. Call `start_photo_processing` once per photo, passing that upload handle and the confirmed settings. One credit is reserved before processing starts. The same file and the same settings return the earlier job and do not charge again.
5. At most three plugin jobs may be queued or processing for the account. If the tool returns `busy`, wait and retry only photos that never started. Do not start a fourth job to get around the cap.
6. If the tool returns `insufficient_credits`, stop. Tell the user to buy credits at [autoshotapp.com](https://autoshotapp.com). Do not start a purchase, invent a checkout, or ask for a card number.

## Poll and show the result

1. Poll `get_photo_processing_status` with the job id `start_photo_processing` returned. A failed status read does not mean the job failed. Retry the status read.
2. An error job refunds the reserved credit. Say that the credit was returned. A reused job did not spend a new credit.
3. When every job in this run has finished, call `show_photo_result` once with those ids.
4. If a result card appears, leave the full-resolution links inside it. Do not copy signed download URLs into the chat.
5. If no card appears, show the preview images the tool returned and tell the user the full files are in Recent photos on [autoshotapp.com](https://autoshotapp.com) for seven days. Download links last one hour.

## Refuse

- Buying credits, opening checkout, or taking payment.
- Deleting history or promising a photo was removed.
- Changing paint, body style, wheels, or anything other than the background, the optional exterior logo, and the optional plate treatment.
- Processing a photo that is not a vehicle with a replaceable background.
- Claiming a plate is anonymized, or that the vehicle is pixel-identical to the original, without the user comparing the result.

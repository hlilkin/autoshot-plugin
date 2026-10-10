---
name: autoshot-account
description: Answer AutoShot setup, credits, saved defaults, dealership logo, and disconnect questions. Use when the user asks how to connect AutoShot, how credits work, how to save photo preferences, where the logo is uploaded, or how to disconnect. Do not use this skill to process a photo or to start a purchase.
---

# AutoShot account

Use this skill for setup and account questions. Photo processing belongs to `process-vehicle-photo`. Nothing in this skill spends a credit.

## First use

Call `get_autoshot_guide` when the user is connecting for the first time or asks what the plugin can do.

Connecting requires an AutoShot account. The consent page sends the user through AutoShot sign-in. Someone without an account creates one on [autoshotapp.com](https://autoshotapp.com) first. New accounts receive the same signup credits as the website. The service is for people 18 or older.

Approve `photos:read`, `photos:process`, and `preferences:write` when Cursor or Grok Bot asks. Read access is required. Processing and saving defaults need the other two scopes.

## Credits

One new photo job reserves one credit, the same credit the website uses. Failed jobs refund that credit. Running the same photo with the same settings again returns the earlier job and does not charge a second credit. A zero balance returns `insufficient_credits` and does not start a job.

There is no purchase tool. Send the user to [autoshotapp.com](https://autoshotapp.com) to buy a credit pack. Do not collect payment details and do not describe a checkout inside the chat.

## Saved defaults and logo

Call `save_photo_preferences` only after the user explicitly asks to save defaults. Saving spends no credit and does not process a photo. Do not claim a background, plate mode, or other choice was saved until that tool succeeds.

Logo plate mode needs a logo already saved on the account page. Uploading a logo is not a tool. The user uploads it at [autoshotapp.com/account](https://autoshotapp.com/account).

## Disconnect

Disconnect is the AutoShot account page: [autoshotapp.com/account](https://autoshotapp.com/account). Revoking the grant stops later tool calls. There is no disconnect tool. Grants last up to 90 days unless the user revokes them sooner.

Cursor stores the OAuth token on its connector backend. AutoShot does not receive the chat transcript. It receives tool arguments, including a photo the user uploads for processing.

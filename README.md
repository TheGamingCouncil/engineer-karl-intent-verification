# Engineer Karl — Privileged Intent Review

Engineer Karl is The Gaming Council's Discord bot for memberflow reporting, staff coordination, recruitment, member support, moderation, and game integrations.

This repository documents Karl's use of **Guild Members** and **Message Content**. **Guild Presences is excluded.**

## Documents

- [Application overview](request.md)
- [Intent-to-function use cases](use-cases.md)
- [Source review and migration status — October 26, 2026](review-status.md)

The use-case document was reformatted on **October 27, 2026**. Each of its 40 entries contains **Privileged intent**, **Discord API called / event received**, **Our function**, **Use case**, **Why we need it**, and **Demo**. The reasons are drafts for TGC to review; every demo field is left for TGC to complete.

## Current source status

The October 26, 2026 source review found **56 native slash-command definitions** in Karl **3.0.0.170**. This count includes ten commands explicitly described as broken, discarded, or not to be used; those commands are excluded as active intent use cases. The [retirement check](review-status.md#retired-and-discouraged-features) identifies them and the two retired command names. Automatic prefix help is disabled, and the reviewed command names have no message-based execution fallback. Restricted commands have execution checks in Karl; their role/user visibility must also be configured through Discord Integrations.

This confirms implementation in the reviewed checkout. **Production deployment, Discord registration, role visibility, and live behavior were not verified in this documentation review.** The [review record](review-status.md) identifies the source revision and existing local edits included in the review.

Command arguments and files submitted through native slash commands are excluded from the Message Content inventory. The remaining entries cover member events, member enumeration, automatic processing of ordinary server messages, and historical message/attachment access. A newly added command-audit worker also detects attempts to use old text commands; that detection still reads ordinary message content, even though the migrated commands do not execute from those messages.

This is working review documentation based on source inspection. It is not a claim of Discord approval or completed live testing. Draft reasons identify relevant limitations and alternatives; screenshots, videos, a completed alternatives assessment, and finalized data-handling answers have not yet been added.

## Discord references

- [Getting Started with Privileged Intent Review](https://docs.discord.com/developers/gateway/getting-started-with-privileged-intent-review)
- [You Might Not Need a Privileged Intent](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent)
- [Application Commands](https://docs.discord.com/developers/interactions/application-commands)
- [Gateway Events](https://docs.discord.com/developers/events/gateway-events)
- [Message Resource](https://docs.discord.com/developers/resources/message)

The document layout follows the application overview and separate use-case document in [Cloe's intent-verification example](https://github.com/fb-sean/cloe-intent-verification). Karl's use cases are documented separately from that example.

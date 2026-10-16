# Engineer Karl — Privileged Intent Review

Engineer Karl is The Gaming Council's Discord bot for memberflow reporting, staff coordination, recruitment, member support, moderation, and game integrations.

This repository documents Karl's use of **Guild Members** and **Message Content**. **Guild Presences is excluded.**

## Documents

- [Application overview](request.md)
- [Intent-to-function use cases](use-cases.md)

## Migration status

TGC plans to migrate Karl's prefix commands to Discord's native slash commands, with command visibility restricted to the appropriate roles. The migration is **planned, not completed**. The use-case tables describe the access that would remain after that migration, with the other workflows preserved.

Command arguments and files submitted through native slash commands are excluded from the Message Content request. The remaining entries cover member events, member enumeration, automatic processing of ordinary server messages, and historical message/attachment access.

This is an initial documentation draft based on source review. It is not a claim of Discord approval or completed live testing. Screenshots, videos, and finalized data-handling answers have not yet been added.

## Discord references

- [Getting Started with Privileged Intent Review](https://docs.discord.com/developers/gateway/getting-started-with-privileged-intent-review)
- [You Might Not Need a Privileged Intent](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent)
- [Application Commands](https://docs.discord.com/developers/interactions/application-commands)
- [Gateway Events](https://docs.discord.com/developers/events/gateway-events)
- [Message Resource](https://docs.discord.com/developers/resources/message)

The document layout follows the application overview and separate use-case document in [Cloe's intent-verification example](https://github.com/fb-sean/cloe-intent-verification). Karl's use cases are documented separately from that example.

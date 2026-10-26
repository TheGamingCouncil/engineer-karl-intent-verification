# Engineer Karl — Application Overview

## What does the application do?

Engineer Karl supports The Gaming Council's Discord community and staff. It records membership changes, reports memberflow, coordinates staff work, tracks acknowledgments, reports recruitment activity, maintains contribution and PTO records, handles support tickets and moderation evidence, and integrates Discord identities and permissions with TGC's game services.

Memberflow is central to TGC's staff coordination. Staff use Karl's scheduled growth/decline reports and recruitment context to assess community health and organize recruitment work. Karl's memberflow alerts can address the responsible staff roles and enter the acknowledgment workflow, which identifies expected responders and missing acknowledgments. Losing this coordination would severely impair TGC's ability to organize recruitment and sustain the community.

## Which privileged intents are included?

- **Guild Members**
- **Message Content**

**Guild Presences is excluded.**

## Native command implementation

The October 26, 2026 review of Karl 3.0.0.170 found 56 native slash-command definitions, including ten commands explicitly described as broken, discarded, or not to be used. Those discouraged features are excluded as active intent use cases; see the [retirement check](review-status.md#retired-and-discouraged-features). The reviewed command migration is implemented in source, including interaction arguments, native attachment options, execution access checks, and startup registration safeguards. Production registration and live role/user visibility have not been verified by this documentation review.

The [use-case tables](use-cases.md) describe the remaining source dependencies. Receiving a slash command's arguments or uploaded files is not included as a Message Content use case. The source still parses attempted old text commands for audit receipts; this is separate from executing commands. See the [review record](review-status.md).

## Guild Members

Karl uses member join, leave, and update events for membership records, nickname/role accountability, and ESO sync-key revocation. It uses complete member and role populations for ACK recipient snapshots, staff eligibility, zero-activity reporting, contribution participation, staffing ratios, and PTO administration. The tables identify each event or member-enumeration operation, Karl function, and associated use case.

## Message Content

The remaining documented uses concern ordinary server messages: edit/delete records, passive processing of bridged ESO chatter, preserving staff-announcement text in ACK audits, historical channel/thread archives, ticket discussion archives, historical attachment retrieval, image-evidence preservation during compromised-account response, and detection of attempted old text commands for command-audit receipts.

Incoming DMs and Karl's own messages are not presented as requiring Message Content. Merely deleting a message by author and time is also not presented as requiring Message Content; the compromised-account entry concerns reading and preserving its image attachments.

## Command-audit records

The current source adds a local SQLite receipt ledger and a notification outbox. Receipts record the invocation kind, invocation ID, UTC time, user ID, guild/channel IDs when present, command name, and notification state. Slash receipts come from interaction data and do not require Message Content. Attempted text-command receipts use the message parser and record a recognized command name or `unknown`.

The receipt-building code does not store command arguments, raw message bodies, attachment contents/URLs, or interaction tokens. This describes the command-audit worker only: other Karl systems, including moderation evidence, tickets, and ACK records, have their own storage behavior. Production retention, encryption at rest, backup practices, and deletion handling are not established by this source review.

## Supporting use cases and evidence

See [Intent-to-function use cases](use-cases.md) for the four-column inventory.

This overview is a draft, not a submitted application. Alternatives analysis, runtime screenshots/videos, and verified privacy, retention, security, and data-handling answers will be added as the review documentation is completed. This repository does not assert that every listed source dependency independently establishes eligibility for intent access.

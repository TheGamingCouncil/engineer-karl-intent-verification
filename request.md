# Engineer Karl — Application Overview

## What does the application do?

Engineer Karl supports The Gaming Council's Discord community and staff. It records membership changes, reports memberflow, coordinates staff work, tracks acknowledgments, reports recruitment activity, maintains contribution and PTO records, handles support tickets and moderation evidence, and integrates Discord identities and permissions with TGC's game services.

Memberflow is central to TGC's staff coordination. Staff use Karl's scheduled growth/decline reports and recruitment context to assess community health and organize recruitment work. Karl's memberflow alerts can address the responsible staff roles and enter the acknowledgment workflow, which identifies expected responders and missing acknowledgments. Losing this coordination would severely impair TGC's ability to organize recruitment and sustain the community.

## Which privileged intents are included?

- **Guild Members**
- **Message Content**

**Guild Presences is excluded.**

## Planned command migration

TGC plans to replace Karl's message-prefix commands with Discord's native slash commands and restrict command visibility by role. This work has not yet been implemented or live-verified.

The [use-case tables](use-cases.md) describe the remaining access after that migration. Receiving a slash command's arguments or uploaded files is not included as a Message Content use case.

## Guild Members

Karl uses member join, leave, and update events for membership records, nickname/role accountability, and ESO sync-key revocation. It uses complete member and role populations for ACK recipient snapshots, staff eligibility, zero-activity reporting, contribution participation, staffing ratios, and PTO administration. The tables identify each event or member-enumeration operation, Karl function, and associated use case.

## Message Content

After the planned slash-command migration, the remaining documented uses concern ordinary server messages: edit/delete records, passive processing of bridged ESO chatter, preserving staff-announcement text in ACK audits, historical channel/thread archives, ticket discussion archives, historical attachment retrieval, and image-evidence preservation during compromised-account response.

Incoming DMs and Karl's own messages are not presented as requiring Message Content. Merely deleting a message by author and time is also not presented as requiring Message Content; the compromised-account entry concerns reading and preserving its image attachments.

## Supporting use cases and evidence

See [Intent-to-function use cases](use-cases.md) for the four-column inventory.

This overview is a draft, not a submitted application. Alternatives analysis, runtime screenshots/videos, and verified privacy, retention, security, and data-handling answers will be added as the review documentation is completed. This repository does not assert that every listed source dependency independently establishes eligibility for intent access.

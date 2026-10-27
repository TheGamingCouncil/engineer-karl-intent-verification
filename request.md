# Engineer Karl — Application Overview

## What does the application do?

Engineer Karl supports The Gaming Council's Discord community and staff. It records membership changes, reports memberflow, coordinates staff work, tracks acknowledgments, reports recruitment activity, maintains contribution and PTO records, handles support tickets and moderation evidence, and integrates Discord identities and permissions with TGC's game services.

Memberflow is central to TGC's staff coordination. Staff use Karl's scheduled growth/decline reports and recruitment context to assess community health and organize recruitment work. Karl's memberflow alerts can address the responsible staff roles and enter the acknowledgment workflow, which identifies expected responders and missing acknowledgments. Losing this coordination would severely impair TGC's ability to organize recruitment and sustain the community.

## Which privileged intents are included?

- **Guild Members**
- **Message Content**

## Guild Members

Karl uses member join, leave, and update events for membership records, nickname/role accountability, and ESO sync-key revocation. It uses complete member and role populations for ACK recipient snapshots, staff eligibility, zero-activity reporting, contribution participation, staffing ratios, and PTO administration.

## Message Content

Karl reads ordinary server messages for edit/delete records, passive processing of bridged ESO chatter, preserving staff-announcement text in ACK audits, historical channel/thread archives, ticket discussion archives, historical attachment retrieval, image-evidence preservation during compromised-account response, and detection of attempted old text commands for command-audit receipts.

The compromised-account use case concerns reading and preserving image attachments before cleanup. Message removal by author and time does not itself require Message Content.

## Command-audit records

Karl maintains a command receipt ledger and a notification outbox. Receipts record the invocation kind, invocation ID, UTC time, user ID, guild/channel IDs when present, command name, and notification state. Slash receipts come from interaction data and do not require Message Content. Attempted text-command receipts use the message parser and record a recognized command name or `unknown`.

Command receipts do not store command arguments, raw message bodies, attachment contents/URLs, or interaction tokens. Other workflows, including moderation evidence, tickets, and ACK records, retain the information described in their respective use cases.

## Supporting use cases and evidence

See [Privileged-intent use cases](use-cases.md) for each function, its Discord API or event dependency, its purpose, and the reason for access.

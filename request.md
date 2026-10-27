# Engineer Karl — Application Overview

## What does the application do?

Engineer Karl supports The Gaming Council's Discord community and staff. It records membership changes, reports memberflow, coordinates staff work, tracks acknowledgments, reports recruitment activity, maintains contribution and PTO records, handles support tickets and moderation evidence, and integrates Discord identities and permissions with TGC's game services.

## Recruitment, ROs, and memberflow

TGC staff make cold approaches to recruit people into the in-game guilds. Staff then reach out to existing guild members who are not in Discord, invite them into the community, and build relationships with them. TGC calls this second stage ROs, or reach-outs.

Karl provides nightly recruitment and whisper leaderboards, a nightly RO leaderboard, and a nightly memberflow report. The activity reports show the work staff recorded; memberflow provides growth or decline feedback alongside that work. Leadership uses these reports to assign responsibilities, identify gaps, and assess whether recruitment and community-building efforts are accompanied by growth. Memberflow alerts can also enter the staff acknowledgment workflow.

See [Recruitment, ROs, and memberflow](use-cases.md#recruitment-ros-and-memberflow) for the shared purpose and the functions that support this workflow.

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

See [Privileged-intent use cases](use-cases.md) for the workflows and their functions. Each workflow explains its operational purpose; each function identifies its intent, Discord API or event, use case, specific reason for access, and demo.

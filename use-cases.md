# Engineer Karl — Privileged Intent Use Cases

## Workflows

| Workflow | Privileged intents |
|---|---|
| [Recruitment, ROs, and memberflow](#recruitment-ros-and-memberflow) | Guild Members; Message Content |
| [Staff coordination and acknowledgments](#staff-coordination-and-acknowledgments) | Guild Members; Message Content |
| [Member identity, role history, and records](#member-identity-role-history-and-records) | Guild Members |
| [Contribution contests, staffing, and PTO](#contribution-contests-staffing-and-pto) | Guild Members |
| [Game-service access and public leader identities](#game-service-access-and-public-leader-identities) | Guild Members |
| [Message records, archives, tickets, and evidence](#message-records-archives-tickets-and-evidence) | Message Content |
| [Command-attempt auditing](#command-attempt-auditing) | Message Content |

Roster-based functions use the shared member cache supplied through **Request Guild Members** and maintained by member events. Individual member lookups and interaction-provided member data are not automatically privileged operations. **Message Content** concerns protected message fields in Gateway events and REST responses, subject to Discord's [documented exceptions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent).

## Recruitment, ROs, and memberflow

**Why this workflow matters:** After TGC's leadership returned from deployment, previously active in-game guilds had lost much of their active membership. Rebuilding them requires coordinated staff work at two stages: recruiting people into the in-game guilds, then reaching out to those members to bring them into Discord and build a closely connected community. We call that second stage ROs, or reach-outs.

Karl's nightly recruitment and whisper leaderboards show the recruitment work staff recorded. The nightly RO leaderboard shows the work connecting existing guild members with Discord and the community. The nightly memberflow report provides growth or decline feedback alongside those activity reports. Guild-specific tags, including TGC1–TGC5, TDC, and TNC, help staff associate Discord members with their in-game guilds.

These reports are project-management tools for leadership: they help staff identify neglected work, decide where to focus recruitment and ROs, and assess whether their efforts are accompanied by growth. Membership changes and role classifications provide information that activity submissions alone cannot supply. The functions below support that workflow; the FF-specific join breakdown is identified separately from overall memberflow.

### Cold approaches and recruitment activity

#### GM-15 — Recruitment-session reporting population

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_recruitment_message_member_ids`

**Use case:** Determine which current role holders qualify for recruitment-session reporting.

**Why we need it:** The recruitment-session report needs the current membership of its configured roles, alongside explicitly included members, rather than only people with recorded activity.

**Demo:**

#### GM-16 — Zero-whisper staff and consecutive-zero tracking

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_whisper_zero_members`

**Use case:** Identify eligible staff with zero recorded whispers for hourly/nightly reports and consecutive-zero-night tracking.

**Why we need it:** A zero-whisper report requires the eligible staff population as well as activity records; otherwise staff with no recorded work could disappear from the report.

**Demo:**

### ROs: connecting existing guild members with Discord

#### GM-14 — RO leaderboard eligibility

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_qualifying_leaderboard_member_ids`

**Use case:** Determine which current staff qualify for the RO leaderboard.

**Why we need it:** Current qualifying-role membership determines the RO leaderboard population; historical duty records alone do not establish who is eligible today.

**Demo:**

### Recruitment-related in-game chatter

#### MC-03 — Recruitment credit from bridged ESO chatter

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `blast_credit`

**Use case:** Recognize the `=D` recruitment marker in ordinary bridged ESO chatter and record the associated staff duty.

**Why we need it:** The recruitment marker and ESO account name are inside the bridged text. The Discord author identifies the bridge sender, not the staff member named in that text.

**Demo:**

#### MC-04 — Recruitment-related chatter alerts

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `blast_credit`

**Use case:** Detect configured recruitment-related words such as "stop," "spam," or "invite" in bridged chatter and alert staff with the message context.

**Why we need it:** Recognizing recruitment-related words and showing staff the surrounding discussion requires the message text, not just its author and message ID.

**Demo:**

#### GM-25 — ESO-name to Discord-member fallback

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `find_user_id_by_nickname`

**Use case:** Use the roster fallback to match an ESO name from bridged chatter to a Discord member.

**Why we need it:** The current fallback scans the roster when the known-name database cannot resolve an ESO name. Member search is an alternative for specific name lookups.

**Demo:**

### Daily memberflow and guild-specific joins

#### GM-01 — Member arrivals

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_ADD`

**Our function:** `on_member_join`

**Use case:** Record who joined and when for membership history and memberflow analysis.

**Why we need it:** The join event identifies each arriving member without requiring an interaction. An aggregate count cannot tell staff who joined.

**Demo:**

#### GM-03 — Member departures

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_REMOVE`

**Our function:** `on_member_remove`

**Use case:** Record who left and when for membership history and memberflow analysis.

**Why we need it:** The departure event identifies who left and when. A total member count cannot distinguish individual departures from arrivals.

**Demo:**

#### GM-05 — FF joins in the daily memberflow report

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_count_ff_joins_for_date`, `send_memberflow_report`

**Use case:** Identify members who joined today and currently hold the FF guild role for the daily memberflow report.

**Why we need it:** The FF breakdown requires join dates and current FF-role membership across the roster. A server-wide count cannot identify that population.

**Demo:**

## Staff coordination and acknowledgments

**Why this workflow matters:** Staff need to receive instructions, acknowledge them, and follow through on assigned work. This includes the response to memberflow alerts as well as other staff responsibilities. Karl establishes who was expected to respond and reports who has not acknowledged, so silence does not make someone disappear from the record. Individual notifications and bounded role reminders support the same coordination work.

### Announcement audiences and missing acknowledgments

#### GM-12 — Expected recipients for staff acknowledgments

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_snapshot_recipients`

**Use case:** Identify every member expected to acknowledge a role-directed staff announcement, including memberflow alerts.

**Why we need it:** The expected audience must include every human member of the addressed roles. Reaction data only identifies people who responded.

**Demo:**

#### GM-13 — Missing acknowledgments

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_snapshot_recipients`, `_build_status_content`

**Use case:** Identify expected recipients who have not acknowledged, including members who never react.

**Why we need it:** Identifying nonresponders requires the complete original recipient snapshot. The status formatter compares saved recipients with acknowledgments rather than requesting another roster.

**Demo:**

#### MC-05 — Original announcement text in ACK audits

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `check_ack_audit_trigger`

**Use case:** Preserve the original text of ordinary staff announcements in their acknowledgment audits. Karl-authored memberflow alerts are exempt from this content requirement.

**Why we need it:** The audit needs the instruction that staff were asked to acknowledge. Role mentions and reactions do not preserve that text; Karl already knows its own memberflow-alert text.

**Demo:**

### Individual notifications and reminders

#### GM-17 — Individual notifications to a role

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `karl_ping`

**Use case:** Identify and notify each human member of a specified role individually.

**Why we need it:** Individual notifications require enumerating the role's human members and excluding bots. A single role mention is an alternative notification method.

**Demo:**

#### GM-18 — Reminder role-size limit

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `reminder_mentions`

**Use case:** Check complete role membership against the reminder recipient limit.

**Why we need it:** Karl requests a complete member cache to check the 100-member role limit; an incomplete roster could make a large role appear small.

**Demo:**

## Member identity, role history, and records

**Why this workflow matters:** Staff need to recognize members as names and roles change, understand changes to responsibility and access, and maintain member and ESO qualification records. Automatic updates cover people who never run a command. Keeping the membership directory, identity history, and role records together lets staff follow the same person across Discord and TGC's other services.

### Joining and leaving identities

#### GM-02 — New-member nickname and identity setup

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_ADD`

**Our function:** `set_initial_nickname`

**Use case:** Initialize a new member's nickname and identity record when they join.

**Why we need it:** The join event starts nickname and identity setup for new members who have not interacted with Karl; editing the nickname itself is not the privileged operation.

**Demo:**

#### GM-04 — Departure identity and staff notification

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_REMOVE`

**Our function:** `set_alias_record`, `left_message`

**Use case:** Preserve the departing member's final identity and notify staff of the departure.

**Why we need it:** The departure handler needs the member identity available at departure; a later user lookup cannot recover the former server nickname and role context.

**Demo:**

### Nickname and role changes

#### GM-06 — Nickname-change history

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `log_member_update`

**Use case:** Record nickname changes, including the previous name, new name, and available actor information.

**Why we need it:** The update event supplies the old and new nickname; a lookup after the change only returns the current name.

**Demo:**

#### GM-07 — Role-change history

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `log_member_update`

**Use case:** Record role additions/removals and the member's before/after role sets.

**Why we need it:** Before-and-after member roles identify which assignments changed, including changes affecting people who are not interacting with Karl.

**Demo:**

#### GM-08 — Configured self-role-change alert

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `Chex_stop_it`

**Use case:** Detect the configured unauthorized self-role-change scenario and alert staff after checking the actor.

**Why we need it:** The member update starts the role-change check; Karl then verifies the audit-log actor before sending the configured self-role-change alert.

**Demo:**

### Member directories, roles, and ESO qualifications

#### GM-09 — Discord member directory and ranks

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_sync_discord_users`

**Use case:** Synchronize the member population's Discord identities and rank classifications into TGC's database.

**Why we need it:** The directory needs identities and current rank roles for the member population, including members absent from existing SQL records or command interactions.

**Demo:**

#### GM-10 — ESO qualifications from Discord roles

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_sync_eso_users`

**Use case:** Synchronize members' DPS, healer, tank, and parse qualifications from Discord roles into ESO records.

**Why we need it:** The synchronization needs qualification roles across the roster; an interaction only supplies its caller's member data.

**Demo:**

#### GM-11 — Database role records

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `update_roles`

**Use case:** Maintain database records of each member's assigned Discord roles.

**Why we need it:** Current roster and role assignments let Karl compare every member with the stored role record, including members who never submit commands.

**Demo:**

## Contribution contests, staffing, and PTO

**Why this workflow matters:** Contribution and PTO administration must cover the current eligible staff population, including people with no recorded work or recent transactions. Staff roles determine participation, categories, access, staffing ratios, and PTO entitlement. Karl uses those current populations so records of past activity do not become the sole definition of who is included.

### Contest participation, categories, and staffing

#### GM-19 — Contest participation, including zero contributions

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_build_contribution_embed_for_window`

**Use case:** Determine the contest population, including eligible members with no contribution records that week.

**Why we need it:** Current membership, eligible roles, and participation settings establish who belongs in the contest, including participants with no contribution records.

**Demo:**

#### GM-20 — Contest participant categories

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_contribution_category`, `build_contribution_embed`

**Use case:** Classify participants using their current staff, contributor, coordinator, or raid-leader roles.

**Why we need it:** Current roles determine each participant's category even when they have not submitted another contribution. This function consumes the shared member cache.

**Demo:**

#### GM-21 — Staff Wanted population ratios

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_member_ids_with_roles`, `build_staff_wanted_embed`

**Use case:** Calculate officer/team populations for staff-to-member ratios in recruitment posts.

**Why we need it:** Officer-to-team ratios require the complete populations of the configured roles, including people without recent activity.

**Demo:**

#### GM-22 — Contest-channel access

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members`; `GUILD_MEMBER_UPDATE` — roster-based calculation and role-change trigger.

**Our function:** `sync_contribution_contest_permissions`

**Use case:** Maintain individual contest-channel access according to current roles and participation settings.

**Why we need it:** The eligible role population and member updates drive individual contest access. Editing permission overwrites itself is not the privileged operation.

**Demo:**

### PTO awards and balance reports

#### GM-23 — Weekly PTO awards

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `karl_PTO_weekly_update`

**Use case:** Find every eligible staff member and award their highest applicable weekly PTO entitlement without stacking role awards.

**Why we need it:** Karl must enumerate every eligible staff role and select each member's highest award, including staff who did not interact with Karl that week.

**Demo:**

#### GM-24 — Staff PTO balance report

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `karl_pto_staff`

**Use case:** List PTO balances for the current staff-role population, including members without recent activity.

**Why we need it:** Current staff-role membership identifies whose balances belong in the report; wallet activity alone may omit inactive staff or include former role holders.

**Demo:**

## Game-service access and public leader identities

**Why this workflow matters:** TGC's connected services need to reflect changes in Discord membership and roles. ESO sync access should be revoked promptly when a member leaves or loses the required access, and public leader identities should follow current names and role styling. Member events provide prompt updates alongside the periodic checks described below.

### ESO sync access

#### GM-26 — Revoke ESO sync access on departure

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_REMOVE`

**Our function:** `revoke_tgc_sync_key_on_departure`

**Use case:** Revoke a departing member's ESO sync keys and stop their sitting reminders.

**Why we need it:** The departure event promptly starts key revocation and sitting-reminder cleanup. Hourly reconciliation also checks key holders, so the event avoids waiting for that fallback.

**Demo:**

#### GM-27 — Revoke ESO sync access after role-based access loss

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `revoke_tgc_sync_key_on_permission_removal`

**Use case:** Revoke ESO sync access when a member loses the required parent-channel access.

**Why we need it:** A member-role update lets Karl compare parent-channel access before and after the change and promptly revoke lost access. Hourly reconciliation provides a fallback.

**Demo:**

### Public leader identities

#### GM-28 — Public leader identity and styling refresh

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_ADD`, `GUILD_MEMBER_UPDATE`, `GUILD_MEMBER_REMOVE`

**Our function:** `monitor_public_leader_styles`

**Use case:** Trigger refreshed public leader identities and styling following membership changes.

**Why we need it:** Member events prompt earlier identity and styling refreshes. The worker also performs individual member lookups and refreshes after a five-minute timeout; those paths do not require this intent.

**Demo:**

## Message records, archives, tickets, and evidence

**Why this workflow matters:** Staff need the substance of a conversation when reviewing edits, deletions, support cases, and compromised accounts. Channels also need to be reusable without losing their prior discussion and files. These workflows preserve available text and attachments before they are changed, cleared, or removed; a record of message IDs and authors alone would not preserve the conversation or evidence.

### Message edits and deletions

#### MC-01 — Edited-message records

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`, `MESSAGE_UPDATE`

**Our function:** `on_message`, `on_message_edit`

**Use case:** Preserve received human-authored text and attachment context, then report what changed in an edited message.

**Why we need it:** The edit record needs the original and updated wording and attachment context; author and timestamp metadata do not show what changed.

**Demo:**

#### MC-02 — Deleted-message records

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE` followed by `MESSAGE_DELETE`

**Our function:** `on_message`, `on_message_delete`

**Use case:** Forward the available cached text and attachments of a deleted human-authored message to the staff log.

**Why we need it:** The deletion event contains no deleted text or files. Karl needs previously received content in its message cache; later history requests cannot recover the deleted message.

**Demo:**

### Channel archives, reuse, and attachment extraction

#### MC-06 — Channel and thread archives

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `karl_archive`

**Use case:** Archive historical human-authored channel/thread text and attachments with author, date, and source context.

**Why we need it:** The archive needs historical conversation text and attachments. The slash command supplies source and destination channels, not the earlier messages' contents.

**Demo:**

#### MC-07 — Archive before clearing a channel

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `clear_channel`, `karl_clear`

**Use case:** Preserve ordinary conversation text and attachments through the archive pipeline before clearing the source.

**Why we need it:** Preserving the conversation before clearing requires its historical text and files. Message deletion alone does not require content, but does not create the archive.

**Demo:**

#### MC-08 — Archive before reusing a raid or event channel

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `karl_reset`

**Use case:** Preserve ordinary conversation text and attachments from an eligible raid/event channel before resetting it for reuse.

**Why we need it:** The reset workflow must read the previous event's discussion and files before clearing the channel for its next leader.

**Demo:**

#### MC-10 — Historical thread attachment download

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `download_thread_files`

**Use case:** Download files attached to ordinary historical thread messages.

**Why we need it:** Extracting previously shared files requires historical attachment records and download information; a native upload option only supplies the newly submitted file.

**Demo:**

### Ticket closure

#### MC-09 — Ticket discussion archive on closure

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `_archive_ticket_channel`, `karl_close`

**Use case:** Preserve ordinary human-authored discussion and attachments in ticket channels and child threads before closure.

**Why we need it:** Archiving human-authored staff discussion requires content from the server-side ticket channel and child threads. DMs and Karl's own forwarded copies are separate exceptions.

**Demo:**

### Compromised-account evidence

#### MC-11 — Image evidence before compromised-account cleanup

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `karl_compromised`

**Use case:** Collect and preserve image attachments from a compromised account's recent server messages before removing those messages.

**Why we need it:** Preserving image evidence requires inspecting historical attachments and downloading the images before removal. Author and time metadata cannot supply the image bytes.

**Demo:**

## Command-attempt auditing

**Why this workflow matters:** Staff use command receipts to review attempts to use Karl, including old text-command attempts that no longer execute. Those attempts originate outside native interactions, so their detection is a separate message-reading use case.

### Old text-command attempts

#### MC-12 — Auditing attempted old text commands

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `CommandAudit.on_message`, `CommandAudit.record_prefix`

**Use case:** Detect attempted old text commands in ordinary server messages and save a command-name receipt for staff auditing, even though migrated commands no longer execute through that path. Arguments, raw message bodies, attachments, and tokens are not stored in these receipts.

**Why we need it:** Recognizing attempted text commands requires reading their names in ordinary messages. Native interaction receipts need no Message Content; removing text-attempt auditing would remove this dependency.

**Demo:**

## Discord API references

- [Member events and Request Guild Members](https://docs.discord.com/developers/events/gateway-events)
- [Guild Members intent and individual-member lookup distinctions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#guild-members-intent)
- [Message Content fields and exceptions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#message-content-intent)
- [Get Channel Messages](https://docs.discord.com/developers/resources/message#get-channel-messages)
- [Native application commands](https://docs.discord.com/developers/interactions/application-commands)

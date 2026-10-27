# Engineer Karl — Privileged Intent Use Cases

These **40 use cases** are based on the **October 26, 2026** review of Karl **3.0.0.170**. The six-field format and draft reasons were added on **October 27, 2026** for TGC to review and improve. Every **Demo** field is left for TGC to complete.

Native slash-command inputs and uploaded files are excluded from Message Content. Guild Presences is excluded entirely. The [retirement check](review-status.md#retired-and-discouraged-features) identifies retired and discouraged commands excluded from this inventory. Scheduled memberflow and automatic contest maintenance are separate from the discouraged manual commands.

Code links point to the reviewed committed revision. The [review record](review-status.md) identifies local edits included in that review and the source-only verification boundary.

## Guild Members

For roster-based entries, **Request Guild Members** describes how the full shared member cache is obtained. Most listed functions consume `guild.members`, `role.members`, or that cache rather than sending another request themselves. Member updates keep the cached role assignments current. Individual lookups and interaction-provided member data are not automatically privileged operations. See Discord's [member-intent distinctions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#guild-members-intent).

### Membership and memberflow

#### GM-01 — Member arrivals

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_ADD`

**Our function:** `on_member_join` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Record who joined and when for membership history and memberflow analysis.

**Why we need it:** Staff need to know who joined and when, including members who never interact with Karl. The join event starts that membership record automatically. An aggregate server count cannot identify individual arrivals or provide the same membership trail alongside memberflow reports.

**Demo:** _To be added by TGC._

#### GM-02 — New-member nickname and identity setup

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_ADD`

**Our function:** `set_initial_nickname` — [database_management.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/database_management.py)

**Use case:** Initialize a new member's nickname and identity record when they join.

**Why we need it:** This setup runs when a member arrives, without requiring them to find and submit a command. Receiving the join event supplies the member and starts the automatic setup. Editing the nickname is not itself the operation that requires the privileged intent.

**Demo:** _To be added by TGC._

#### GM-03 — Member departures

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_REMOVE`

**Our function:** `on_member_remove` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Record who left and when for membership history and memberflow analysis.

**Why we need it:** Staff need to know which member left and when. A change in the total member count cannot distinguish individual departures and arrivals, and the departing member cannot be expected to interact with Karl after leaving.

**Demo:** _To be added by TGC._

#### GM-04 — Departure identity and staff notification

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_REMOVE`

**Our function:** `set_alias_record`, `left_message` — [database_management.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/database_management.py), [disc_messages.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/disc_messages.py)

**Use case:** Preserve the departing member's final identity and notify staff of the departure.

**Why we need it:** Staff need to recognize the departing member by the identity they used in TGC. The departure handler uses the member context available at departure. A later user lookup cannot recover their former server nickname and role context.

**Demo:** _To be added by TGC._

#### GM-05 — FF joins in the daily memberflow report

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_count_ff_joins_for_date`, `send_memberflow_report` — [memberflow.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/memberflow.py)

**Use case:** Identify members who joined today and currently hold the FF guild role for the daily memberflow report.

**Why we need it:** The FF join figure gives staff recruitment context in the daily memberflow report. Karl checks join dates and current FF roles across the roster, including people who never use Karl. A server-wide member count cannot supply that breakdown. This requirement concerns the FF classification, not every memberflow calculation.

**Demo:** _To be added by TGC._

### Identity, role history, and database records

#### GM-06 — Nickname-change history

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `log_member_update` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Record nickname changes, including the previous name, new name, and available actor information.

**Why we need it:** Staff need the old and new nickname to follow identity changes. Looking up a member afterward returns the current name, not the before-and-after state used by this handler. Audit-log actor information supplements the member event.

**Demo:** _To be added by TGC._

#### GM-07 — Role-change history

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `log_member_update` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Record role additions/removals and the member's before/after role sets.

**Why we need it:** Roles determine responsibilities and access. The update event supplies the member state Karl uses to record additions and removals. Checking only command callers would miss role changes affecting people who are not interacting with Karl.

**Demo:** _To be added by TGC._

#### GM-08 — Configured self-role-change alert

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `Chex_stop_it` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Detect the configured unauthorized self-role-change scenario and alert staff after checking the actor.

**Why we need it:** The configured alert must start when the member changes roles. Karl compares the old and new role sets and verifies the audit-log actor before notifying staff. The member event supplies the trigger; the audit log supplies the actor check.

**Demo:** _To be added by TGC._

#### GM-09 — Discord member directory and ranks

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_sync_discord_users` — [database_management.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/database_management.py)

**Use case:** Synchronize the member population's Discord identities and rank classifications into TGC's database.

**Why we need it:** This job synchronizes the server population, not just command users or existing SQL records. It needs the roster and current roles to build identity and rank records for members who have never interacted with Karl.

**Demo:** _To be added by TGC._

#### GM-10 — ESO qualifications from Discord roles

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_sync_eso_users` — [database_management.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/database_management.py)

**Use case:** Synchronize members' DPS, healer, tank, and parse qualifications from Discord roles into ESO records.

**Why we need it:** The job reads qualification roles across the Discord roster and writes the corresponding ESO records. One interaction only provides its caller's roles; this synchronization also covers members who do not use Karl. The discarded Velothi commands are not the reason for this entry.

**Demo:** _To be added by TGC._

#### GM-11 — Database role records

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `update_roles` — [database_management.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/database_management.py)

**Use case:** Maintain database records of each member's assigned Discord roles.

**Why we need it:** The scheduled job compares each member's current roles with their stored record. It needs the roster so people who never submit commands are not omitted. The SQL record alone cannot tell Karl which Discord assignments have changed.

**Demo:** _To be added by TGC._

### Staff coordination and recruitment

#### GM-12 — Expected recipients for staff acknowledgments

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_snapshot_recipients` — [ack_audit.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ack_audit.py)

**Use case:** Identify every member expected to acknowledge a role-directed staff announcement, including memberflow alerts.

**Why we need it:** Staff coordination depends on knowing everyone expected to acknowledge an instruction, including memberflow alerts. Karl snapshots the human members of the addressed roles. A reaction list only identifies responders; it cannot establish the complete expected audience.

**Demo:** _To be added by TGC._

#### GM-13 — Missing acknowledgments

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_snapshot_recipients`, `_build_status_content` — [ack_audit.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ack_audit.py)

**Use case:** Identify expected recipients who have not acknowledged, including members who never react.

**Why we need it:** A missing-ACK report must include people who never react or run a command. Karl compares responses against the complete original recipient snapshot. Reactions alone cannot identify nonresponders. The status formatter consumes that saved snapshot rather than making another roster request.

**Demo:** _To be added by TGC._

#### GM-14 — RO leaderboard eligibility

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_qualifying_leaderboard_member_ids` — [ro_leaderboard.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ro_leaderboard.py)

**Use case:** Determine which current staff qualify for the RO leaderboard.

**Why we need it:** Historical duty records do not establish who holds the qualifying staff roles today. Karl uses current role membership to select the eligible population, including people who have never invoked the reporting command.

**Demo:** _To be added by TGC._

#### GM-15 — Recruitment-session reporting population

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_recruitment_message_member_ids` — [ro_leaderboard.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ro_leaderboard.py)

**Use case:** Determine which current role holders qualify for recruitment-session reporting.

**Why we need it:** The reporting population includes the configured recruitment roles as well as explicitly included members. The role roster identifies current participants even when they have no recent activity or interactions. Logged activity alone does not establish that population.

**Demo:** _To be added by TGC._

#### GM-16 — Zero-whisper staff and consecutive-zero tracking

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_whisper_zero_members` — [ro_leaderboard.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ro_leaderboard.py)

**Use case:** Identify eligible staff with zero recorded whispers for hourly/nightly reports and consecutive-zero-night tracking.

**Why we need it:** Staff need to see who did no recorded whisper work, not just who submitted activity. Karl compares the eligible role population against the activity records and applies configured exclusions. Without the roster, a member with no activity could disappear from the report entirely.

**Demo:** _To be added by TGC._

#### GM-17 — Individual notifications to a role

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `karl_ping` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Identify and notify each human member of a specified role individually.

**Why we need it:** The owner command deliberately sends individual member mentions and skips bots. That behavior requires enumerating the role's human members. A single role mention is another notification method; this dependency concerns the chosen individual notifications, not sending a message in general.

**Demo:** _To be added by TGC._

#### GM-18 — Reminder role-size limit

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `reminder_mentions` — [reminders.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/reminders.py)

**Use case:** Check complete role membership against the reminder recipient limit.

**Why we need it:** Karl permits reminder role mentions only when the role has at most 100 members. It explicitly requests the full member cache when needed so an incomplete list cannot make a large role appear small. The slash interaction supplies the reminder text; this dependency is the role-population check.

**Demo:** _To be added by TGC._

### Contribution contests, staffing, and PTO

#### GM-19 — Contest participation, including zero contributions

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_build_contribution_embed_for_window` — [contribution_recruitment.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/contribution_recruitment.py)

**Use case:** Determine the contest population, including eligible members with no contribution records that week.

**Why we need it:** The contribution query identifies recorded work, but does not establish all current eligible participants. Karl combines the current member population, roles, and participation settings so eligible people with zero contributions are still included.

**Demo:** _To be added by TGC._

#### GM-20 — Contest participant categories

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_contribution_category`, `build_contribution_embed` — [contribution_recruitment.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/contribution_recruitment.py)

**Use case:** Classify participants using their current staff, contributor, coordinator, or raid-leader roles.

**Why we need it:** Staff, contributors, coordinators, and raid leaders can change roles without submitting another contribution. Current member roles determine their contest categories. The formatter reads the shared member cache; it is not making a separate full-roster request.

**Demo:** _To be added by TGC._

#### GM-21 — Staff Wanted population ratios

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `_member_ids_with_roles`, `build_staff_wanted_embed` — [contribution_recruitment.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/contribution_recruitment.py)

**Use case:** Calculate officer/team populations for staff-to-member ratios in recruitment posts.

**Why we need it:** The staffing post compares officer and team populations. Those totals require the membership of the configured roles, including people with no recent activity. Neither the person requesting an update nor the contribution ledger supplies those complete populations.

**Demo:** _To be added by TGC._

#### GM-22 — Contest-channel access

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members`; `GUILD_MEMBER_UPDATE` — roster-based calculation and role-change trigger.

**Our function:** `sync_contribution_contest_permissions` — [contribution_recruitment.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/contribution_recruitment.py)

**Use case:** Maintain individual contest-channel access according to current roles and participation settings.

**Why we need it:** Karl computes individual access from current role membership and participation settings, then updates it following relevant role changes. Past participants alone would miss eligible nonparticipants and could retain outdated access. The dependency is discovering and maintaining the eligible population, not the permission-edit API itself.

**Demo:** _To be added by TGC._

#### GM-23 — Weekly PTO awards

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `karl_PTO_weekly_update` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** Find every eligible staff member and award their highest applicable weekly PTO entitlement without stacking role awards.

**Why we need it:** Every eligible staff member should receive the weekly award, whether or not they used a command that week. Karl examines the eligible role populations and chooses each person's highest applicable award. One caller's interaction cannot identify all the other staff entitled to an award.

**Demo:** _To be added by TGC._

#### GM-24 — Staff PTO balance report

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `karl_pto_staff` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** List PTO balances for the current staff-role population, including members without recent activity.

**Why we need it:** This report covers the current staff team, including people without recent transactions. Karl starts with staff-role membership and then reads balances. Wallet activity alone could omit inactive staff or include people who no longer hold a qualifying role.

**Demo:** _To be added by TGC._

### Game integration access and public leader identities

#### GM-25 — ESO-name to Discord-member fallback

**Privileged intent:** Guild Members

**Discord API called / event received:** `Request Guild Members` — full member cache used by this function.

**Our function:** `find_user_id_by_nickname` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Use the roster fallback to match an ESO name from bridged chatter to a Discord member.

**Why we need it:** When the known-name database cannot resolve an ESO name, the current fallback scans Discord nicknames and usernames. This implementation needs the roster. Discord member search could be investigated as a replacement, so this is not a claim that name lookup inherently requires the intent.

**Demo:** _To be added by TGC._

#### GM-26 — Revoke ESO sync access on departure

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_REMOVE`

**Our function:** `revoke_tgc_sync_key_on_departure` — [tgc_addon_sync.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/tgc_addon_sync.py)

**Use case:** Revoke a departing member's ESO sync keys and stop their sitting reminders.

**Why we need it:** Leaving TGC should promptly trigger key revocation and stop sitting reminders without waiting for another interaction. Karl also has an hourly reconciliation worker. The departure event therefore provides timely cleanup rather than the only possible way to detect that a key holder has left.

**Demo:** _To be added by TGC._

#### GM-27 — Revoke ESO sync access after role-based access loss

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_UPDATE`

**Our function:** `revoke_tgc_sync_key_on_permission_removal` — [tgc_addon_sync.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/tgc_addon_sync.py)

**Use case:** Revoke ESO sync access when a member loses the required parent-channel access.

**Why we need it:** A role change can remove the parent-channel access required for sync. Karl compares the old and new permissions, revokes the key, and stops sitting reminders. The event avoids waiting for hourly reconciliation. This covers member-role changes; other channel-permission changes need not produce a member update.

**Demo:** _To be added by TGC._

#### GM-28 — Public leader identity and styling refresh

**Privileged intent:** Guild Members

**Discord API called / event received:** `GUILD_MEMBER_ADD`, `GUILD_MEMBER_UPDATE`, `GUILD_MEMBER_REMOVE`

**Our function:** `monitor_public_leader_styles` — `public_leader_styles.py` (local source; not committed at the linked revision)

**Use case:** Trigger refreshed public leader identities and styling following membership changes.

**Why we need it:** Member events prompt an earlier refresh after changes to a leader's nickname, assigned roles, or server membership. The worker already fetches known members individually and has a five-minute refresh timeout. The intent supports the prompt event trigger here, not the individual lookups or periodic refresh.

**Demo:** _To be added by TGC._

## Message Content

This intent controls protected message fields in Gateway events and REST responses; it does not enable delivery of message events themselves. The entries concern ordinary server messages outside Discord's exceptions. DMs, Karl-authored messages, messages mentioning Karl, and message-command selections are subject to those [documented exceptions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#message-content-intent). Historical reads use [Get Channel Messages](https://docs.discord.com/developers/resources/message#get-channel-messages).

### Message edits and deletions

#### MC-01 — Edited-message records

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`, `MESSAGE_UPDATE`

**Our function:** `on_message`, `on_message_edit` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Preserve received human-authored text and attachment context, then report what changed in an edited message.

**Why we need it:** Staff need the actual old and new wording and attachment context to understand an edit. Author IDs and timestamps do not show what changed. Karl must receive those protected fields before it can preserve them; its cache only covers messages received while the bot is running.

**Demo:** _To be added by TGC._

#### MC-02 — Deleted-message records

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE` followed by `MESSAGE_DELETE`

**Our function:** `on_message`, `on_message_delete` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Forward the available cached text and attachments of a deleted human-authored message to the staff log.

**Why we need it:** The deletion event does not contain the deleted text or files. Karl must receive those fields beforehand so its cached message can supply the staff log. A later history request cannot recover a deleted message, and forwarding an attachment can still fail if its file is no longer available.

**Demo:** _To be added by TGC._

### Recruitment monitoring and staff announcements

#### MC-03 — Recruitment credit from bridged ESO chatter

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `blast_credit` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Recognize the `=D` recruitment marker in ordinary bridged ESO chatter and record the associated staff duty.

**Why we need it:** The recruitment marker and ESO account name are inside the bridged message text. Karl reads them to match the account and record the duty. The Discord author alone identifies the bridge sender, not the staff member named in the message. Native slash inputs do not supply these incoming messages.

**Demo:** _To be added by TGC._

#### MC-04 — Recruitment-related chatter alerts

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `blast_credit` — [on_bot_event.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/on_bot_event.py)

**Use case:** Detect configured recruitment-related words such as "stop," "spam," or "invite" in bridged chatter and alert staff with the message context.

**Why we need it:** Staff use these alerts to notice recruitment-related discussion in the bridge channel. Karl reads the words to decide whether to alert and includes the surrounding text so staff can interpret it. Message IDs and author metadata cannot identify the keyword match or explain what was said.

**Demo:** _To be added by TGC._

#### MC-05 — Original announcement text in ACK audits

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `check_ack_audit_trigger` — [ack_audit.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ack_audit.py)

**Use case:** Preserve the original text of ordinary staff announcements in their acknowledgment audits. Karl-authored memberflow alerts are exempt from this content requirement.

**Why we need it:** The audit needs to show what staff were asked to read or do. Role mentions and reaction data can identify recipients and responses, but do not preserve the instruction itself. This requirement concerns ordinary staff-authored announcements; Karl already knows the text of its own memberflow alerts.

**Demo:** _To be added by TGC._

### Archives, tickets, and evidence

#### MC-06 — Channel and thread archives

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `karl_archive` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** Archive historical human-authored channel/thread text and attachments with author, date, and source context.

**Why we need it:** The archive must preserve the conversation and attached material, not merely authors and dates. Karl reads historical content and attachments from the source. The slash command supplies the source and destination, not the contents of the older messages.

**Demo:** _To be added by TGC._

#### MC-07 — Archive before clearing a channel

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `clear_channel`, `karl_clear` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** Preserve ordinary conversation text and attachments through the archive pipeline before clearing the source.

**Why we need it:** Staff clear a channel for fresh use while preserving its conversation and files. Karl needs the historical content before clearing the source. Deleting messages alone does not require their content, but would not produce the preserved record this workflow is intended to create.

**Demo:** _To be added by TGC._

#### MC-08 — Archive before reusing a raid or event channel

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `karl_reset` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** Preserve ordinary conversation text and attachments from an eligible raid/event channel before resetting it for reuse.

**Why we need it:** The channel needs to be ready for the next leader without losing the previous event's discussion and attachments. Karl reads that historical material before resetting the channel. The reset command's interaction does not contain the earlier conversation.

**Demo:** _To be added by TGC._

#### MC-09 — Ticket discussion archive on closure

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `_archive_ticket_channel`, `karl_close` — [ticketing.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/ticketing.py)

**Use case:** Preserve ordinary human-authored discussion and attachments in ticket channels and child threads before closure.

**Why we need it:** Closing a ticket should preserve the human-authored staff discussion and files in its server-side channel and child threads. Those messages exist independently of the close command. Incoming DMs and Karl's own forwarded copies are exceptions; this dependency concerns ordinary server-side discussion.

**Demo:** _To be added by TGC._

#### MC-10 — Historical thread attachment download

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `download_thread_files` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** Download files attached to ordinary historical thread messages.

**Why we need it:** The command extracts files already attached to messages throughout a thread. Karl needs the attachment records and download information for ordinary historical messages. A native attachment option provides a newly submitted file, not all the files previously shared in the thread.

**Demo:** _To be added by TGC._

#### MC-11 — Image evidence before compromised-account cleanup

**Privileged intent:** Message Content

**Discord API called / event received:** `Get Channel Messages` — `GET /channels/{channel.id}/messages`, through channel/thread history.

**Our function:** `karl_compromised` — [karl_commands.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/karl_commands.py)

**Use case:** Collect and preserve image attachments from a compromised account's recent server messages before removing those messages.

**Why we need it:** Staff need image evidence preserved before Karl removes the compromised account's messages. Karl inspects historical attachments, identifies images, and downloads their bytes. Author and time metadata can support message removal, but cannot supply the images for the evidence record.

**Demo:** _To be added by TGC._

### Legacy text-command attempt auditing

#### MC-12 — Auditing attempted old text commands

**Privileged intent:** Message Content

**Discord API called / event received:** `MESSAGE_CREATE`

**Our function:** `CommandAudit.on_message`, `CommandAudit.record_prefix` — [command_audit.py](https://github.com/TheGamingCouncil/engineer_karl_disc/blob/6992eaad67e0be78ad7c050829b79c4d0d8b89e5/discord_bot/command_audit.py)

**Use case:** Detect attempted old text commands in ordinary server messages and save a command-name receipt for staff auditing, even though migrated commands no longer execute through that path. Arguments, raw message bodies, attachments, and tokens are not stored in these receipts.

**Why we need it:** The listener still records attempts to type old commands into ordinary server messages. It reads enough text to recognize the command name, even though the migrated commands no longer execute there. Native interaction receipts need no Message Content; dropping old-text-attempt auditing would remove this particular dependency.

**Demo:** _To be added by TGC._

## Native interaction uses excluded from Message Content

Wallet commands, duty submissions, PTO claims, moderation submissions, staff ticket replies, reminders, and ESO export uploads use native interaction arguments or attachment options. Those inputs do not need Message Content. Native command-audit receipts use interaction metadata and also do not need it. Historical-message reads and ordinary-message monitoring above are separate from the interaction that may start a task.

Role-gated native command execution uses interaction-provided member roles and Karl's access checks; this is not another Guild Members use case. The contribution reaction whitelist uses reaction-provided member information when available and a cache fallback; it is not counted as another standalone privileged-intent requirement.

## Discord API references

- [Member events and Request Guild Members](https://docs.discord.com/developers/events/gateway-events)
- [Guild Members intent and individual-member lookup distinctions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#guild-members-intent)
- [Message Content fields and exceptions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#message-content-intent)
- [Get Channel Messages](https://docs.discord.com/developers/resources/message#get-channel-messages)
- [Native application commands](https://docs.discord.com/developers/interactions/application-commands)

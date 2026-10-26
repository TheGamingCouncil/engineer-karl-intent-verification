# Engineer Karl — Privileged Intent Use Cases

These tables were rechecked against Karl **3.0.0.170** on **October 26, 2026**. The reviewed checkout defines **56 native slash commands**, including ten explicitly discouraged commands that are excluded as active use cases. The tables describe the remaining privileged-intent dependencies; they do not claim that the reviewed source has been deployed or that Discord registration/live behavior has been verified. TGC function names refer to the current source implementation. See the [review record](review-status.md).

Command arguments and files submitted through slash commands are excluded from Message Content. Guild Presences is excluded entirely. These entries are source-based use cases; runtime screenshots/videos and alternatives analysis are not yet included.

The [retirement check](review-status.md#retired-and-discouraged-features) excludes the old budget, Velothi parsing, event-logging, and other discouraged command uses. Broken manual `calcflow` commands are not the scheduled memberflow system; the discouraged `contribution_post` command is not the automatic contest worker. Both background systems still have startup callers. No function in the tables below was found removed in the current source.

## Guild Members

`Request Guild Members` refers to the complete member population used by Karl's member/role cache. Member-object reads for a known user are not automatically separate privileged-intent requirements.

| Privileged intent | Discord API event / endpoint | TGC function | Use case |
|---|---|---|---|
| Guild Members | `GUILD_MEMBER_ADD` | `on_member_join` | Record who joined and when for membership history and memberflow analysis. |
| Guild Members | `GUILD_MEMBER_ADD` | `set_initial_nickname` | Initialize a new member's nickname and identity record when they join. |
| Guild Members | `GUILD_MEMBER_REMOVE` | `on_member_remove` | Record who left and when for membership history and memberflow analysis. |
| Guild Members | `GUILD_MEMBER_REMOVE` | `set_alias_record`, `left_message` | Preserve the departing member's final identity and notify staff of the departure. |
| Guild Members | `Request Guild Members` | `_count_ff_joins_for_date`, `send_memberflow_report` | Identify members who joined today and currently hold the FF guild role for the daily memberflow report. |
| Guild Members | `GUILD_MEMBER_UPDATE` | `log_member_update` | Record nickname changes, including the previous name, new name, and available actor information. |
| Guild Members | `GUILD_MEMBER_UPDATE` | `log_member_update` | Record role additions/removals and the member's before/after role sets. |
| Guild Members | `GUILD_MEMBER_UPDATE` | `Chex_stop_it` | Detect the configured unauthorized self-role-change scenario and alert staff after checking the actor. |
| Guild Members | `Request Guild Members` | `_sync_discord_users` | Synchronize the member population's Discord identities and rank classifications into TGC's database. |
| Guild Members | `Request Guild Members` | `_sync_eso_users` | Synchronize members' DPS, healer, tank, and parse qualifications from Discord roles into ESO records. |
| Guild Members | `Request Guild Members` | `update_roles` | Maintain database records of each member's assigned Discord roles. |
| Guild Members | `Request Guild Members` | `_snapshot_recipients` | Identify every member expected to acknowledge a role-directed staff announcement, including memberflow alerts. |
| Guild Members | `Request Guild Members` | `_snapshot_recipients`, `_build_status_content` | Identify expected recipients who have not acknowledged, including members who never react. |
| Guild Members | `Request Guild Members` | `_qualifying_leaderboard_member_ids` | Determine which current staff qualify for the RO leaderboard. |
| Guild Members | `Request Guild Members` | `_recruitment_message_member_ids` | Determine which current role holders qualify for recruitment-session reporting. |
| Guild Members | `Request Guild Members` | `_whisper_zero_members` | Identify eligible staff with zero recorded whispers for hourly/nightly reports and consecutive-zero-night tracking. |
| Guild Members | `Request Guild Members` | `_build_contribution_embed_for_window` | Determine the contest population, including eligible members with no contribution records that week. |
| Guild Members | `Request Guild Members` | `_contribution_category`, `build_contribution_embed` | Classify participants using their current staff, contributor, coordinator, or raid-leader roles. |
| Guild Members | `Request Guild Members` | `_member_ids_with_roles`, `build_staff_wanted_embed` | Calculate officer/team populations for staff-to-member ratios in recruitment posts. |
| Guild Members | `Request Guild Members`; `GUILD_MEMBER_UPDATE` | `sync_contribution_contest_permissions` | Maintain individual contest-channel access according to current roles and participation settings. |
| Guild Members | `Request Guild Members` | `karl_PTO_weekly_update` | Find every eligible staff member and award their highest applicable weekly PTO entitlement without stacking role awards. |
| Guild Members | `Request Guild Members` | `karl_pto_staff` | List PTO balances for the current staff-role population, including members without recent activity. |
| Guild Members | `Request Guild Members` | `karl_ping` | Identify and notify each human member of a specified role individually. |
| Guild Members | `Request Guild Members` | `reminder_mentions` | Check complete role membership against the reminder recipient limit. |
| Guild Members | `Request Guild Members` | `find_user_id_by_nickname` | Use the roster fallback to match an ESO name from bridged chatter to a Discord member. |
| Guild Members | `GUILD_MEMBER_REMOVE` | `revoke_tgc_sync_key_on_departure` | Revoke a departing member's ESO sync keys and stop their sitting reminders. |
| Guild Members | `GUILD_MEMBER_UPDATE` | `revoke_tgc_sync_key_on_permission_removal` | Revoke ESO sync access when a member loses the required parent-channel access. |
| Guild Members | `GUILD_MEMBER_ADD`, `GUILD_MEMBER_UPDATE`, `GUILD_MEMBER_REMOVE` | `monitor_public_leader_styles` | Trigger refreshed public leader identities and styling following membership changes. |

## Message Content

Message Content controls protected fields within message objects; it is not the intent that delivers `MESSAGE_CREATE`, `MESSAGE_UPDATE`, or `MESSAGE_DELETE` events themselves. Deleted-message content in this table comes from Karl's received-message cache, not from the deletion event payload.

| Privileged intent | Discord API event / endpoint | TGC function | Use case |
|---|---|---|---|
| Message Content | `MESSAGE_CREATE`, `MESSAGE_UPDATE` | `on_message`, `on_message_edit` | Preserve received human-authored text and attachment context, then report what changed in an edited message. |
| Message Content | `MESSAGE_CREATE` followed by `MESSAGE_DELETE` | `on_message`, `on_message_delete` | Forward the available cached text and attachments of a deleted human-authored message to the staff log. |
| Message Content | `MESSAGE_CREATE` | `blast_credit` | Recognize the `=D` recruitment marker in ordinary bridged ESO chatter and record the associated staff duty. |
| Message Content | `MESSAGE_CREATE` | `blast_credit` | Detect configured recruitment-related words such as "stop," "spam," or "invite" in bridged chatter and alert staff with the message context. |
| Message Content | `MESSAGE_CREATE` | `check_ack_audit_trigger` | Preserve the original text of ordinary staff announcements in their acknowledgment audits. Karl-authored memberflow alerts are exempt from this content requirement. |
| Message Content | `Get Channel Messages` | `karl_archive` | Archive historical human-authored channel/thread text and attachments with author, date, and source context. |
| Message Content | `Get Channel Messages` | `clear_channel`, `karl_clear` | Preserve ordinary conversation text and attachments through the archive pipeline before clearing the source. |
| Message Content | `Get Channel Messages` | `karl_reset` | Preserve ordinary conversation text and attachments from an eligible raid/event channel before resetting it for reuse. |
| Message Content | `Get Channel Messages` | `_archive_ticket_channel`, `karl_close` | Preserve ordinary human-authored discussion and attachments in ticket channels and child threads before closure. |
| Message Content | `Get Channel Messages` | `download_thread_files` | Download files attached to ordinary historical thread messages. |
| Message Content | `Get Channel Messages` | `karl_compromised` | Collect and preserve image attachments from a compromised account's recent server messages before removing those messages. |
| Message Content | `MESSAGE_CREATE` | `CommandAudit.on_message`, `CommandAudit.record_prefix` | Detect attempted old text commands in ordinary server messages and save a command-name receipt for staff auditing, even though migrated commands no longer execute through that path. Arguments, raw message bodies, attachments, and tokens are not stored in these receipts. |

## Native interaction uses excluded from the Message Content inventory

Wallet commands, duty submissions, PTO claims, moderation submissions, staff ticket replies, reminders, and ESO export uploads use native interaction arguments/attachment options in the reviewed source. They do not require Message Content for those inputs. Native command-audit receipts use `INTERACTION_CREATE` metadata and likewise do not require Message Content. Historical-message, automatic-monitoring, and attempted text-command auditing uses above remain separate.

Role-gated native command execution uses interaction-provided member roles and Karl's access checks; this is not itself another Guild Members use case. The contribution reaction whitelist uses reaction-provided member information when available and a cache fallback; it is not listed as a new standalone privileged-intent requirement. Incoming DMs, Karl-authored messages, and individual member lookups remain subject to Discord's documented exceptions/distinctions.

## Discord API references

- [Member events and Request Guild Members](https://docs.discord.com/developers/events/gateway-events)
- [Guild Members intent and individual-member lookup distinctions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#guild-members-intent)
- [Message Content fields and exceptions](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent#message-content-intent)
- [Get Channel Messages](https://docs.discord.com/developers/resources/message#get-channel-messages)
- [Native application commands](https://docs.discord.com/developers/interactions/application-commands)

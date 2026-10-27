# Engineer Karl — Privileged Intent Use Cases

- [Recruitment, ROs, and memberflow](#recruitment-ros-and-memberflow)
- [Staff coordination and acknowledgments](#staff-coordination-and-acknowledgments)
- [Member identity, role history, and records](#member-identity-role-history-and-records)
- [Contribution contests, staffing, and PTO](#contribution-contests-staffing-and-pto)
- [Game-service access and public leader identities](#game-service-access-and-public-leader-identities)
- [Message records, archives, tickets, and evidence](#message-records-archives-tickets-and-evidence)
- [Command-attempt auditing](#command-attempt-auditing)

## Recruitment, ROs, and memberflow

**Intent:** Guild Members

**Whispers = our recruitment.**

**ROs = Reach Outs, which is staff trying to reel in recruited members to participate in the community more.**

**Staff Duties/Points = Our way of tracking staff tasks completed, quantifying value into "points", and appreciating/recognizing them for selfless service.**

**Why:** After leadership returned from deployment, our once-active in-game guilds had nearly emptied. Nightly whisper and RO leaderboards show the staff's work. Memberflow reports show whether the community is growing or shrinking. We use these reports to assign work and assess results.

| ID | Use case | Why access is needed |
|---|---|---|
| GM-15 | Report recruitment work by responsible staff | We need to know which staff are expected to recruit so Karl can report their work. |
| GM-16 | Report staff with zero whispers | The roster reveals staff members that are neglecting roles they committed to. |
| GM-14 | Nightly RO leaderboard | Nightly we create a leaderboard of staff members that have reached out to new members. To do this, we need to know who is responsible to be doing this community outreach. |
| GM-25 | Match bridged ESO names | The fallback scans Discord names; member search is an alternative. |
| GM-01 | Record Discord arrivals | Shows staff the names of people joining Discord each day, so they can follow up with those new members. |
| GM-03 | Record Discord departures | Leave events identify who left and when. We require Discord membership, so if they leave our Discord, we kick them in-game as well. It also helps us identify when someone like a staff or event leader leaves, that way we can cover things they were supposed to be doing. |
| GM-05 | Count FF-tagged joins | Join dates and FF roles identify the daily guild-specific population. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| GM-15 | Guild Members: `Request Guild Members` | `_recruitment_message_member_ids` |
| GM-16 | Guild Members: `Request Guild Members` | `_whisper_zero_members` |
| GM-14 | Guild Members: `Request Guild Members` | `_qualifying_leaderboard_member_ids` |
| GM-25 | Guild Members: `Request Guild Members` | `find_user_id_by_nickname` |
| GM-01 | Guild Members: `GUILD_MEMBER_ADD` | `on_member_join` |
| GM-03 | Guild Members: `GUILD_MEMBER_REMOVE` | `on_member_remove` |
| GM-05 | Guild Members: `Request Guild Members` | `_count_ff_joins_for_date`, `send_memberflow_report` |

</details>

**Demo:**


## Staff coordination and acknowledgments

**Intent:** Guild Members; Message Content

**Why:** Staff need clear instructions and follow-through. Karl identifies the expected audience, records acknowledgments, and shows who has not responded—including to memberflow alerts.

| ID | Use case | Why access is needed |
|---|---|---|
| GM-12 | Set expected ACK recipients | Role membership identifies everyone expected to respond. |
| GM-13 | Report missing ACKs | The saved recipient roster includes people who never react. |
| MC-05 | Preserve ACK instructions | Announcement text records what staff acknowledged; Karl-authored alerts are exempt. |
| GM-17 | Notify role members individually | The roster identifies human recipients. A role ping is an alternative. |
| GM-18 | Limit reminder role size | Complete membership prevents undercounting the 100-member limit. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| GM-12 | Guild Members: `Request Guild Members` | `_snapshot_recipients` |
| GM-13 | Guild Members: `Request Guild Members` | `_snapshot_recipients`, `_build_status_content` |
| MC-05 | Message Content: `MESSAGE_CREATE` | `check_ack_audit_trigger` |
| GM-17 | Guild Members: `Request Guild Members` | `karl_ping` |
| GM-18 | Guild Members: `Request Guild Members` | `reminder_mentions` |

</details>

**Demo:**


## Member identity, role history, and records

**Intent:** Guild Members

**Why:** Staff must recognize members as names and roles change. Karl maintains identity history, role records, and ESO qualifications, including for members who never run commands.

| ID | Use case | Why access is needed |
|---|---|---|
| GM-02 | Set new-member nicknames | Join events start setup without requiring a command. |
| GM-04 | Record departing members' identities | Departure context preserves the former server identity. |
| GM-06 | Log nickname changes | Before-and-after names show what changed. |
| GM-07 | Log role changes | Before-and-after role sets identify additions and removals. |
| GM-08 | Alert on configured self-role changes | Member events trigger the check; audit logs verify the actor. |
| GM-09 | Sync member identities and ranks | The roster includes members who never use commands. |
| GM-10 | Sync ESO qualifications | Current roles identify members' DPS, healer, tank, and parse qualifications. |
| GM-11 | Sync database role records | Current assignments reveal changes to stored roles. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| GM-02 | Guild Members: `GUILD_MEMBER_ADD` | `set_initial_nickname` |
| GM-04 | Guild Members: `GUILD_MEMBER_REMOVE` | `set_alias_record`, `left_message` |
| GM-06 | Guild Members: `GUILD_MEMBER_UPDATE` | `log_member_update` |
| GM-07 | Guild Members: `GUILD_MEMBER_UPDATE` | `log_member_update` |
| GM-08 | Guild Members: `GUILD_MEMBER_UPDATE` | `Chex_stop_it` |
| GM-09 | Guild Members: `Request Guild Members` | `_sync_discord_users` |
| GM-10 | Guild Members: `Request Guild Members` | `_sync_eso_users` |
| GM-11 | Guild Members: `Request Guild Members` | `update_roles` |

</details>

**Demo:**


## Contribution contests, staffing, and PTO

**Intent:** Guild Members

**Why:** Current roles determine participation, access, staffing ratios, and PTO entitlement. Eligible staff must remain included even when they have no recorded activity.

| ID | Use case | Why access is needed |
|---|---|---|
| GM-19 | Include zero-contribution participants | Roles and participation settings establish eligibility beyond activity records. |
| GM-20 | Classify contest participants | Current roles determine staff and contributor categories. |
| GM-21 | Calculate staffing ratios | Complete officer and team populations provide the counts. |
| GM-22 | Maintain contest-channel access | Current roles and participation settings determine individual access. |
| GM-23 | Award weekly PTO | Role populations identify every eligible member and their highest entitlement. |
| GM-24 | Report staff PTO balances | Current staff roles establish whose balances to include. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| GM-19 | Guild Members: `Request Guild Members` | `_build_contribution_embed_for_window` |
| GM-20 | Guild Members: `Request Guild Members` | `_contribution_category`, `build_contribution_embed` |
| GM-21 | Guild Members: `Request Guild Members` | `_member_ids_with_roles`, `build_staff_wanted_embed` |
| GM-22 | Guild Members: `Request Guild Members`; `GUILD_MEMBER_UPDATE` | `sync_contribution_contest_permissions` |
| GM-23 | Guild Members: `Request Guild Members` | `karl_PTO_weekly_update` |
| GM-24 | Guild Members: `Request Guild Members` | `karl_pto_staff` |

</details>

**Demo:**


## Game-service access and public leader identities

**Intent:** Guild Members; Message Content

**Why:** Karl tracks in-game chat blasts and flags recruitment-related messages. It also revokes lost sync access and refreshes leader identities when membership or roles change.

| ID | Use case | Why access is needed |
|---|---|---|
| MC-03 | Credit in-game chat blasts. | Karl reads messages copied from ESO into Discord, finds `=D`, matches the ESO name to a Discord member, and records duty credit. |
| MC-04 | Alert staff to recruitment-related chat | Karl reads messages copied from ESO into Discord and alerts staff when they contain words such as “invite,” “stop,” or “spam.” |
| GM-26 | Revoke sync keys after departure | Leave events provide prompt cleanup; hourly reconciliation also checks membership. |
| GM-27 | Revoke sync keys after role-based access loss | Member updates provide prompt revocation; hourly reconciliation remains a fallback. |
| GM-28 | Refresh public leader identities | Member events accelerate updates; individual lookups and five-minute refreshes remain available. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| MC-03 | Message Content: `MESSAGE_CREATE` | `blast_credit` |
| MC-04 | Message Content: `MESSAGE_CREATE` | `blast_credit` |
| GM-26 | Guild Members: `GUILD_MEMBER_REMOVE` | `revoke_tgc_sync_key_on_departure` |
| GM-27 | Guild Members: `GUILD_MEMBER_UPDATE` | `revoke_tgc_sync_key_on_permission_removal` |
| GM-28 | Guild Members: `GUILD_MEMBER_ADD`, `GUILD_MEMBER_UPDATE`, `GUILD_MEMBER_REMOVE` | `monitor_public_leader_styles` |

</details>

**Demo:**


## Message records, archives, tickets, and evidence

**Intent:** Message Content

**Why:** Staff need the conversation and files—not just author IDs—when reviewing incidents, closing tickets, or reusing channels. Karl preserves available content before it changes or disappears.

| ID | Use case | Why access is needed |
|---|---|---|
| MC-01 | Record message edits | Text and attachment context show what changed. |
| MC-02 | Record message deletions | Previously cached content supplies what deletion events omit. |
| MC-06 | Archive channels and threads | Historical text and attachments preserve the conversation. |
| MC-07 | Archive before clearing | Content must be read before the source is cleared. |
| MC-08 | Archive before event-channel reuse | Previous discussion and files must be preserved before reset. |
| MC-10 | Download historical thread files | Historical attachment records identify the files to retrieve. |
| MC-09 | Archive closed tickets | Human-authored staff discussion and files need preservation. |
| MC-11 | Preserve compromised-account evidence | Image attachments must be downloaded before message removal. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| MC-01 | Message Content: `MESSAGE_CREATE`, `MESSAGE_UPDATE` | `on_message`, `on_message_edit` |
| MC-02 | Message Content: `MESSAGE_CREATE` followed by `MESSAGE_DELETE` | `on_message`, `on_message_delete` |
| MC-06 | Message Content: `Get Channel Messages` | `karl_archive` |
| MC-07 | Message Content: `Get Channel Messages` | `clear_channel`, `karl_clear` |
| MC-08 | Message Content: `Get Channel Messages` | `karl_reset` |
| MC-10 | Message Content: `Get Channel Messages` | `download_thread_files` |
| MC-09 | Message Content: `Get Channel Messages` | `_archive_ticket_channel`, `karl_close` |
| MC-11 | Message Content: `Get Channel Messages` | `karl_compromised` |

</details>

**Demo:**


## Command-attempt auditing

**Intent:** Message Content

**Why:** Staff audit attempts to use old text commands, even though those commands no longer execute. Receipts retain command metadata without message bodies or arguments.

| ID | Use case | Why access is needed |
|---|---|---|
| MC-12 | Audit old text-command attempts | Recognizing typed command names needs content; native slash receipts do not. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| MC-12 | Message Content: `MESSAGE_CREATE` | `CommandAudit.on_message`, `CommandAudit.record_prefix` |

</details>

**Demo:**


## Discord references

- [Privileged intents and alternatives](https://docs.discord.com/developers/gateway/you-might-not-need-a-privileged-intent)
- [Gateway events](https://docs.discord.com/developers/events/gateway-events)
- [Message history](https://docs.discord.com/developers/resources/message#get-channel-messages)

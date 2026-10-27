# Engineer Karl — Privileged Intent Use Cases

- [Recruitment, ROs, and memberflow](#recruitment-ros-and-memberflow)
- [Staffing and Project Management](#staffing-and-project-management)
- [Member identity, role history, and records](#member-identity-role-history-and-records)
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
| GM-15 | Live reports of recruitment progress by responsible staff throughout the day. | The Discord function call tells us which staff members are in roles that are expected to recruit so we can record/report live progress on their work. |
| GM-16 | We have a nightly leaderboards of staff recruitment accomplished during the day. | The Discord function call allows us to identify the staff members on the team that does this task so we can build this leaderboard. |
| GM-14 | We create a nightly leaderboard of staff members that have reached out to new members. To do this, we need to know who is responsible to be doing this community outreach. | Discord's function call allows us to identify the staff members that are on the team that does community outreach so we can build our leaderboard. |
| GM-01 | We require Discord for guild membership. New members get removed from our guilds in-game if they do not join Discord. This tells the staff when they join so they can promote in-game. It also creates an account that can be linked with our Minecraft server and website for the user. Lastly, it helps staff understand which members are new so they can reach out to and do the knitting of a tightly-knit community. | The Discord function is used to tell us when a new member joins, their developer ID for use as a foreign key in other SQL tables, and what their name is. |
| GM-03 | Leave events identify who left and when. We require Discord membership, so if they leave our Discord, we remove them from out community in-game as well. It also helps us identify when someone like a staff or event leader leaves, that way we can cover things they were supposed to be doing and may not have been handed off. Lastly, it will revoke login access to staff recruitment tools and the website. | The Discord function tells us who leaves, their developer ID which is used as a foreign key linking platforms and SQL tables, and triggers the closure of their TGC login access. |
| GM-05 | Most of our members are coming from The Elder Scrolls Online; however, lately much progress growing has been being made by our Final Fantasy guild. This lets us know how many joined from the FF guild to differentiate progress, credit, and points between the staff teams on the two games. | The Discord function allows us to tell who is a member of our Final Fantasy guild versus who is a member of our Elder Scrolls Online guild. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| GM-15 | Guild Members: `Request Guild Members` | `_recruitment_message_member_ids` |
| GM-16 | Guild Members: `Request Guild Members` | `_whisper_zero_members` |
| GM-14 | Guild Members: `Request Guild Members` | `_qualifying_leaderboard_member_ids` |
| GM-01 | Guild Members: `GUILD_MEMBER_ADD` | `on_member_join` |
| GM-03 | Guild Members: `GUILD_MEMBER_REMOVE` | `on_member_remove` |
| GM-05 | Guild Members: `Request Guild Members` | `_count_ff_joins_for_date`, `send_memberflow_report` |

</details>

**GM-15, GM-16 Demo:**

- &lt;Screenshot: Live Whisper Count&gt;
- &lt;Screenshot: Total Whisper Count&gt;
- &lt;Screenshot: Nightly Whisper Leaderboard&gt;

**GM-14 Demo:**

- &lt;Screenshot: Nightly RO Leaderboard&gt;

**GM-01, GM-03, GM-05 Demo:**

- &lt;Screenshot: Nightly memberflow leaderboard&gt;

## Staffing and Project Management

**Intent:** Guild Members; Message Content

**Why:** Karl helps staff coordinate work, track completed tasks, and recognize contributions. Current roles determine who must be involved in tasks, who should acknowledge important announcements, who belongs in staff reports, and who should be involved in staff contests.

| ID | Use case | Why access is needed |
|---|---|---|
| GM-12 | We require messages of importance to be acknowledged through an "ACK" emoji reaction by staff. This function helps us identify who in our team should be on the hook for reading these messages. | The Discord function tells us who is on the staff team that important messages are going out to and thus should be acknowledging the messages. |
| GM-13 | This is a roster of people that have failed to do the "ACK" reaction mentioned above so we can reach out to the users individually to follow-up with, individually train, etc. | The Discord function allows us to see members that are part of staff teams associated with announcements. |
| MC-05 | If members of a team fail to do their "ACK" reactions to a message, we need to know which message it is. | The Discord function records which message the "ACK" check is based on. |
| GM-17 | This can be used for several purposes. 1 - Let us know who is in a role in a public way, as many members are non-technical and won't know to click on a role or go to server settings. 2 - It will ping members if authorized which can be used to bring people into threads or get their attention individually as an alternative to pinging a role that has role pings disabled. Karl has programming on who is allowed to use this command or not, which is an added level of security than just allowing anyone to @ mention a role. | This Discord function identifies which users are in a role or on a team. |
| GM-18 | Karl has a functionality similar to Microsoft-To Do. This prevents users from being able to abuse our ping functionality in here to spam the server recklessly or maliciously. | This Discord functions tells us which roles are too large for &lt;just anyone&gt; to be able to ping so we can stop it. |
| GM-19 | We have a staff contest for in-game gold and public appreciation of staff. This function identifies the people who should be involved in the contest, minus people who have opted out. | This Discord function tells us who are in staff team roles for initial display in the contest they can opt-out of. |
| GM-20 | In the staff contest mentioned above, this function identifies someone's rank/status (inactive staff, full-time staff, part-time staff, etc) for display in our contest leaderboard post. | This Discord function allows us to get a further breakdown of contest participants by their current status (full-time, part-time, inactive, etc). |
| GM-21 | We have a "staff-wanted" post that explains how understaffed we are, requesting for assistance from members by submitting staff applications. One of the arguments that we use is a live ratio of members to staff. This function comes up with that ratio. | This Discord function allows us to count the number of staff and the number of guild members for accurate officer-to-member ratios in the help-wanted post. |
| GM-22 | We have been getting "hate raids" from a few rival guilds. This function uses configured roles to restrict view access to the staff-contest channel for people we consider high-risk. | This Discord function lists people in roles that we consider to be high-risk so they can be individually blocked from sensitive channels. |
| GM-23 | We have a "PTO" system for staff to be able to pass their expectations even when they are away, for use during times like vacations. This command awards everyone on staff with some PTO to be used in the future. The Discord API is called when identifying who should be given PTO. | This Discord function tells us who in the Discord should be being awarded PTO. |
| GM-24 | This allows us to be able to see the PTO that staff have for use during periods away. | This Discord function tells us who should have PTO so we can generate a report of users' remaining PTO. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| GM-12 | Guild Members: `Request Guild Members` | `_snapshot_recipients` |
| GM-13 | Guild Members: `Request Guild Members` | `_snapshot_recipients`, `_build_status_content` |
| MC-05 | Message Content: `MESSAGE_CREATE` | `check_ack_audit_trigger` |
| GM-17 | Guild Members: `Request Guild Members` | `karl_ping` |
| GM-18 | Guild Members: `Request Guild Members` | `reminder_mentions` |
| GM-19 | Guild Members: `Request Guild Members` | `_build_contribution_embed_for_window` |
| GM-20 | Guild Members: `Request Guild Members` | `_contribution_category`, `build_contribution_embed` |
| GM-21 | Guild Members: `Request Guild Members` | `_member_ids_with_roles`, `build_staff_wanted_embed` |
| GM-22 | Guild Members: `Request Guild Members`; `GUILD_MEMBER_UPDATE` | `sync_contribution_contest_permissions` |
| GM-23 | Guild Members: `Request Guild Members` | `karl_PTO_weekly_update` |
| GM-24 | Guild Members: `Request Guild Members` | `karl_pto_staff` |

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

## Game-service access and public leader identities

**Intent:** Guild Members; Message Content

**Why:** Karl tracks in-game chat blasts and flags recruitment-related messages. It also revokes lost sync access and refreshes leader identities when membership or roles change.

| ID | Use case | Why access is needed |
|---|---|---|
| MC-03 | Credit in-game chat blasts. | Karl reads messages copied from ESO into Discord, finds `=D`, matches the ESO name to a Discord member, and records duty credit. |
| GM-25 | Match ESO names for MC-03 credit | MC-03 needs this name match to award in-game chat-blast credit to the correct staff member. The fallback scans Discord names; member search is an alternative. |
| MC-04 | Alert staff to recruitment-related chat | Karl reads messages copied from ESO into Discord and alerts staff when they contain words such as “invite,” “stop,” or “spam.” |
| GM-26 | Revoke sync keys after departure | Leave events provide prompt cleanup; hourly reconciliation also checks membership. |
| GM-27 | Revoke sync keys after role-based access loss | Member updates provide prompt revocation; hourly reconciliation remains a fallback. |
| GM-28 | Refresh public leader identities | Member events accelerate updates; individual lookups and five-minute refreshes remain available. |

<details>

<summary>Functions and Discord APIs</summary>

| ID | Intent / Discord API | Our function |
|---|---|---|
| MC-03 | Message Content: `MESSAGE_CREATE` | `blast_credit` |
| GM-25 | Guild Members: `Request Guild Members` | `find_user_id_by_nickname` |
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

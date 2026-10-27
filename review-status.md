# Source Review — October 26, 2026

## Reviewed source

- Karl version: **3.0.0.170**.
- Source repository: `TheGamingCouncil/engineer_karl_disc`.
- Source HEAD: `6992eaad67e0be78ad7c050829b79c4d0d8b89e5`.
- Comparison baseline: the October 22 review at `f8ed349bf1598b67e2bb038b20942c19ecfedfe3`.
- Scope: the current working copy, including existing uncommitted source edits in `hourly_whispers.py`, `karl.py`, `ro_leaderboard.py`, and `tgc_addon_sync.py`, and local production modules `access_audit_post.py` and `public_leader_styles.py`.
- The audit parsed all **31 production Python modules**, inspected native registration and command-audit wiring, and rechecked the documented member-event, roster, message, archive, and attachment paths.
- Runtime CSVs, caches, member exports, credentials, keys, and evidence files were not copied into this public repository. No bot source or runtime data was changed by the documentation update.

## Changes since the original documentation

| Area | Current source finding | Documentation update |
|---|---|---|
| Native commands | `slash_commands.register_commands` constructs 56 guild-scoped application commands. `config.py` creates `KarlBot`; `KarlBot.setup_hook` registers the local commands and calls the guarded sync path. | Replaced the future migration assumption with implemented-in-source status. |
| Command inputs | Native callbacks accept interaction arguments and attachment options. The reviewed names no longer have prefix execution fallbacks, and automatic prefix help is disabled. | Kept command inputs and submitted files out of the Message Content inventory. |
| Command changes | `/karl_credit` is merged into `/karl_report` with a CREDIT choice; `/karlkick` is retired in favor of `/karl_kick`. `/help` is native. Mixed-case prefix names have lowercase native names. | Avoided carrying retired names forward as additional intent uses. |
| Access | Native commands have explicit role/user execution rules. Restricted commands default to administrator-only at Discord's registration layer until Integrations overrides are configured; Karl still enforces its execution checks. | Distinguished implemented execution checks from unverified live picker visibility. |
| Registration | Startup compares the local and remote schemas, guards against unreviewed registrations/global conflicts, and skips unchanged definitions. Registration can be explicitly disabled by configuration. | Did not claim that source definitions prove production registration. |
| Command audit | A separate interaction/message listener builds durable SQLite receipts and sends queued staff notifications. | Added one Message Content row for attempted ordinary text-command detection. Native interaction receipts are excluded from privileged-intent requirements. |
| Audit storage | Receipts contain invocation identity/time/location/name metadata and notification state, without command bodies/options, attachment data, or tokens. | Added the source-observed storage behavior without asserting production privacy/security controls. |
| Contest reactions | StaffPlus members are permitted on live contest posts, and the snapshot whitelist also checks StaffPlus. The code uses reaction-provided member data with a cache fallback. | Did not inflate this into a new standalone privileged-intent requirement. |
| Memberflow and staff coordination | The memberflow monitor, scheduled reports, role-directed alerts/ACK recipient snapshots, staff eligibility, and zero-activity population checks remain in the source. | Preserved the existing memberflow/staff coordination use cases. |
| Presence | `intents.presences` remains false. | Guild Presences remains excluded. |
| Changes after October 22 | Native sync uploads accept modular `TGCSync.lua` exports alongside legacy exports and retain upload messages for debugging. Public command output uses standalone messages, and generic completion replies were removed. | These changes do not introduce a privileged-intent use case for interaction inputs. Upload retention is separate from the command-audit ledger's metadata-only storage. |

## Retired and discouraged features

The October 26 follow-up checked source history, the native command manifest and callbacks in `slash_commands.py`, event handlers, and background startup in `karl.py`. Finding a legacy function definition alone was not treated as evidence of current use.

Two command names are explicitly retired in `RETIRED_COMMAND_NAMES`:

| Retired command | Current replacement |
|---|---|
| `karl_credit` | `karl_report` with the CREDIT choice. |
| `karlkick` | `karl_kick`. |

The current `COMMAND_DESCRIPTIONS` explicitly discourages ten further commands:

| Feature | Commands | Source status |
|---|---|---|
| Manual memberflow calculations | `calcflow`, `calcflowestimated` | Broken; do not use. |
| Old report check | `karl_report_check` | Broken; do not use. |
| Budget and bounty display | `karl_budget` | Discarded. |
| Status command | `karl_status` | Broken; do not use. |
| Role-permission display | `check_mfa_perms` | Do not use. |
| Former Velothi parse standards | `vel`, `velcheck` | Legacy; do not use. |
| Event and voice-attendance logging command | `log` | Discarded. |
| Manual contribution-post command | `contribution_post` | Do not use. |

These ten commands still have native callback definitions. They are excluded as active intent use cases because of their explicit source descriptions; this is not a claim that their implementation has been deleted. Neither the retired names nor these discouraged commands appears as a use-case function in the current inventory, so this check did not remove entries.

The scheduled memberflow system is separate: `karl.on_ready` starts `monitor_memberflow`, which calls `send_memberflow_report`. Likewise, it starts `monitor_contribution_post`, which maintains contest posts, staffing posts, and contest-channel permissions independently of the discouraged manual command. Their member and role dependencies remain in the inventory.

Comparing top-level function definitions across the 31 production modules with the October 22 source revision found no removed functions. Every function named in the inventory remains present; the review traced their use through native callbacks, member/message events, background workers, or those workers' helpers. This establishes source wiring, not production usage frequency or successful execution.

## Inventory

- **Guild Members:** 28 use-case entries, retained after rechecking the current functions and event/roster paths.
- **Message Content:** 12 use-case entries, including attempted text-command auditing, which remains wired despite the removal of prefix execution for reviewed commands.
- Each entry uses six fields: **Privileged intent / Discord API called or event received / Our function / Use case / Why we need it / Demo**.

The memberflow report combines scheduled member-count snapshots, FF-specific join classification, and stored RO activity. The Guild Members entries identify member events and complete-population classification/ACK needs; they do not imply that every aggregate count, report send, or database calculation independently requires that intent.

## October 27 format and rationale update

Converted all 40 existing cases into individual six-field entries, grouped by related workflows within each intent. Every case retains its function names and use case, adds a draft reason for the data access, and leaves the demo for TGC. Entry IDs allow TGC to refer to specific reasons while revising them. Code links point to the reviewed committed source; the local public-leader module is explicitly identified as uncommitted.

The rationale distinguishes event triggers from actions such as editing nicknames or permissions, and shared-cache consumers from direct member-list requests. It also identifies the hourly sync-key reconciliation, five-minute public-leader refresh, possible member-search replacement, and native command-audit path so these are not presented as features with no alternatives. TGC will review the reasons and supply the demonstrations.

## Verification boundary

This is a source/documentation review. It does not establish which checkout is running, whether the new commands are deployed/registered, whether Integrations visibility is configured, or whether any feature passed live testing. No production Discord calls, SQL mutations, bot restart, or deployment were performed.

The reformatted documentation was checked for all 40 original function/use-case pairs, six fields per entry, empty demo placeholders, local document links, committed source-file references, and public readback. No new bot-runtime test pass is asserted; the selected local Python interpreter did not have `discord.py` installed during the source review. Existing migration test files were inspected as supporting source, not presented as newly executed evidence.

Screenshots/videos, alternatives analysis, and finalized production privacy/retention/security answers remain to be added. A listed source dependency is not a claim that no alternative exists or that Discord will approve access.

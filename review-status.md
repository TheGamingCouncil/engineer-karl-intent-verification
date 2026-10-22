# Source Review — October 22, 2026

## Reviewed source

- Karl version: **3.0.0.168**.
- Source repository: `TheGamingCouncil/engineer_karl_disc`.
- Source HEAD: `f8ed349bf1598b67e2bb038b20942c19ecfedfe3`.
- Scope: the current working copy, including existing uncommitted source edits in `hourly_whispers.py`, `karl.py`, `ro_leaderboard.py`, and `tgc_addon_sync.py`.
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

## Inventory

- **Guild Members:** 28 use-case rows, retained after rechecking the current functions and event/roster paths.
- **Message Content:** 12 use-case rows, including the new attempted text-command audit row.
- The four columns remain: **Privileged intent / Discord API event or endpoint / TGC function / Use case**.

The memberflow report combines scheduled member-count snapshots, FF-specific join classification, and stored RO activity. The Guild Members rows identify member events and complete-population classification/ACK needs; they do not imply that every aggregate count, report send, or database calculation independently requires that intent.

## Verification boundary

This is a source/documentation review. It does not establish which checkout is running, whether the new commands are deployed/registered, whether Integrations visibility is configured, or whether any feature passed live testing. No production Discord calls, SQL mutations, bot restart, or deployment were performed.

Published documentation was checked for four-column table structure, current function references, local document links, and public readback. No new bot-runtime test pass is asserted; the selected local Python interpreter did not have `discord.py` installed. Existing migration test files were inspected as supporting source, not presented as newly executed evidence.

Screenshots/videos, alternatives analysis, and finalized production privacy/retention/security answers remain to be added. A listed source dependency is not a claim that no alternative exists or that Discord will approve access.

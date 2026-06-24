# Docs Changelog

Log of changes to this repo's own files/structure (CLAUDE.md, STATE.md, README.md, conventions). For changes that mutate the live Home Assistant instance, see `CHANGELOG.md` instead.

## 2026-06-24

1. Created `CLAUDE.md` from two prior per-machine files (`CLAUDE-nuc.md`, `CLAUDE-desktop.md`), which were identical; removed the per-machine files.
2. Added `README.md` explaining the repo's purpose and `.mcp.json` setup for new machines.
3. Added a `Process` section to `CLAUDE.md`: consult the `home-assistant-best-practices` skill before persistent config changes; log server-state mutations to `CHANGELOG.md` and commit immediately.
4. Changed `CHANGELOG.md` entries from bullets to ordered lists, to reflect logical step sequence.
5. Split point-in-time inventory (entity/device counts, areas, automations, helpers, sensors, dashboards) out of `CLAUDE.md` into a new `STATE.md`, read on demand rather than auto-loaded, so stale counts can't silently mislead. `CLAUDE.md` retained only durable conventions/policies.
6. Added this file (`DOCS-CHANGELOG.md`) to track repo/doc changes separately from HA server-state changes.
7. Added `ISSUES.md` for observations flagged during other work but not acted on (status starts `TBD`).
8. Documented in `CLAUDE.md`: always search for consumers before touching any entity/automation (no staging environment); no entity domain gets extra confirmation by default; household context (cleaners fortnightly, pet sitters during holiday mode) explaining the holiday automations' purpose.
9. Documented in `CLAUDE.md`'s Process section: ask clarifying questions one at a time rather than bundling several into one prompt.
10. Added `/state` slash command (`.claude/commands/state.md`) to revalidate `STATE.md` against the live HA instance, correct drift in place (no new sections), flag unresolved findings in `ISSUES.md`, and log the run in this changelog. Added a "Available Commands" section to `README.md`.
11. Added `/conventions` slash command (`.claude/commands/conventions.md`) to audit the live HA instance against `CLAUDE.md` conventions (naming, icons, Hue groups, entity patterns), print findings directly in chat (no report file), and log all findings to `ISSUES.md` since the audit itself doesn't action anything.
12. Added `/issue` slash command (`.claude/commands/issue.md`) to list open `ISSUES.md` entries and work through one at a time, updating its status in place once resolved. Updated `README.md`'s Available Commands list.
13. Documented in `CLAUDE.md`'s Process section: after adding a new Claude Code slash command, add it to `README.md`'s "Available Commands" list.
14. Ran `/state` to revalidate `STATE.md` against the live HA instance. Drift found and corrected: entity count 482→554, automation count 58→67; moved Man Cave from "Garage/External" to "House" (matches live floor data); Room automations 24→28 (added Man Cave Sim Rig On/Off, Kitchen Light Bar On With No Motion/Turned Off); Routine 9→8 (removed `Routine: Bedtime Start`, no longer found live); Alert 6→8 (added Meater Cook Started, Meater Estimated Time Available); System 1→2 (added IKEA Hub Auto-Restart); added `input_boolean.ben_s_ipad_in_use` to Helpers; corrected Key Devices' Main TV entry (`media_player.living_room_tv_2` no longer exists). 5 ambiguous findings (uncategorized `Holiday:`-prefixed automations, `Garage override:` naming, unverifiable "Pool area" sensor, missing `Routine: Bedtime Start`, ambiguous Main TV replacement) logged to `ISSUES.md` rather than guessed at.
15. Renamed the `/issue` slash command to `/issues` (`.claude/commands/issue.md` → `.claude/commands/issues.md`) and updated `README.md`'s Available Commands list.

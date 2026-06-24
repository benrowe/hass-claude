# Docs Changelog

Log of changes to this repo's own files/structure (CLAUDE.md, STATE.md, README.md, conventions). For changes that mutate the live Home Assistant instance, see `CHANGELOG.md` instead.

## 2026-06-24

1. Created `CLAUDE.md` from two prior per-machine files (`CLAUDE-nuc.md`, `CLAUDE-desktop.md`), which were identical; removed the per-machine files.
2. Added `README.md` explaining the repo's purpose and `.mcp.json` setup for new machines.
3. Added a `Process` section to `CLAUDE.md`: consult the `home-assistant-best-practices` skill before persistent config changes; log server-state mutations to `CHANGELOG.md` and commit immediately.
4. Changed `CHANGELOG.md` entries from bullets to ordered lists, to reflect logical step sequence.
5. Split point-in-time inventory (entity/device counts, areas, automations, helpers, sensors, dashboards) out of `CLAUDE.md` into a new `STATE.md`, read on demand rather than auto-loaded, so stale counts can't silently mislead. `CLAUDE.md` retained only durable conventions/policies.
6. Added this file (`DOCS-CHANGELOG.md`) to track repo/doc changes separately from HA server-state changes.

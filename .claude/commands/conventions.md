---
description: Audit the live HA instance against CLAUDE.md conventions and report findings
---

Audit the live Home Assistant instance against the conventions documented in `CLAUDE.md` (Naming Convention, Decision Rules, Icon Guidelines, Hue Groups, Common Entity Patterns). This is read-only against the live instance — don't fix anything yet, just find and report.

## Steps

1. Read `CLAUDE.md` in full for the current conventions.
2. Query the live instance via the `hass` MCP server and check:
   - **Automation naming** — every automation's name should be `{Category}: {Trigger}` where `{Category}` is one of `Routine`, `Mode`, `Presence`, `Alert`, `Device`, `System`, or a known room/area name. Flag anything that doesn't match.
   - **Automation icons** — flag any automation with no icon set at all (objective). Loosely cross-check icon choice against the Icon Guidelines style table, but report style mismatches as "worth a look", not a hard violation — that call is subjective.
   - **Hue Groups** — search for any `is_hue_group` light entity that is not disabled. This is a hard violation of policy.
   - **Common Entity Patterns** — check `light.*`, `climate.*`, `cover.*`, `binary_sensor.*`, `sensor.*` entity_ids against the patterns in CLAUDE.md (`light.<area>_<fixture>`, `climate.<area>_ac`, `cover.garage_door_<n>`, `binary_sensor.<area>_motion`, `sensor.<area>_temperature`/`_illuminance`). Flag entity_ids that clearly don't fit (e.g. stray suffixes, inconsistent area naming).
3. Split findings into two groups:
   - **Violations** — objective, clearly breaks a documented rule.
   - **Worth a look** — judgment calls, possible but not certain issues.
4. Print the full findings directly in your response (group by category, include entity/automation IDs) — do not write a separate report file.
5. Below the findings, give a numbered suggestion list of what could be done about each violation/worth-a-look item, so the user can pick what (if anything) to act on next.
6. Append every finding (violations and worth-a-look items) to `ISSUES.md` under `## TBD`, since nothing has been actioned yet — this run is audit-only. Use one line per finding, specific enough to act on later (entity/automation ID, what's wrong).
7. Commit the `ISSUES.md` update (e.g. `git add ISSUES.md && git commit -m "..."`). No `CHANGELOG.md`/`DOCS-CHANGELOG.md` entry needed — `ISSUES.md` is itself the record.

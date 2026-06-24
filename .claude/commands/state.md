---
description: Revalidate STATE.md against the live Home Assistant instance and update it in place
---

Revalidate `STATE.md` against the live Home Assistant instance via the `hass` MCP server, and correct any drift. This is a read-then-reconcile pass — don't change the live HA instance, only `STATE.md`.

## Steps

1. Read `STATE.md` in full.
2. For each existing section, query the live instance and compare:
   - **Overview** (entity/device/integration/area/automation counts) — use `ha_get_overview` (or equivalent counts from `ha_search`/`ha_list_floors_areas`).
   - **Areas/Rooms** — `ha_list_floors_areas`.
   - **Disabled Hue Group Entities** — `ha_search(domain_filter="light")` + `ha_get_entity` to confirm which `is_hue_group` entities exist and their `disabled_by` status.
   - **Key Integrations** — confirm the integrations list and device counts still hold.
   - **Automation Categories** — confirm the automations listed still exist under the stated category/name; check for new automations not yet categorized here.
   - **Helpers & Toggles** — confirm input_booleans/numbers/texts/datetimes listed still exist (`ha_config_list_helpers`).
   - **Sensors** — spot-check the sensors listed still exist.
   - **Dashboards** — confirm dashboard paths still resolve.
   - **Key Devices** — confirm the entity_ids listed still exist and behave as described.
3. Update `STATE.md` in place to reflect the live state. **Keep the existing layout and section structure exactly as-is — this is a content refresh, not a restructure.** Don't add new sections; if something doesn't fit an existing section, note it as drift instead (see step 4).
4. For anything you find that's drifted but can't be cleanly resolved by just editing `STATE.md` (e.g. an entity referenced in an automation that no longer exists, an ambiguous rename, something requiring a judgment call), add it to `ISSUES.md` under `TBD` rather than guessing.
5. Append a summary entry to `DOCS-CHANGELOG.md` (today's date, ordered list, continuing the existing numbering if today's section already exists) describing what was checked and what drifted/was corrected — or note "no drift found" if STATE.md was already accurate.
6. Commit the changes (`STATE.md`, `DOCS-CHANGELOG.md`, and `ISSUES.md` if touched) per the commit convention in `CLAUDE.md`'s Process section.

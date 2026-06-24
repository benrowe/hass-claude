---
name: ha-rename
description: Propose a better name for a Home Assistant device or entity, then rename it live and patch every consumer once approved. Input is a device name/id or an entity_id (e.g. light.lr_1, or a device name like "Living Room Plug 3"). This skill both proposes the rename and (once approved) applies it and patches consumers — it is not read-only. Trigger when the user wants to rename, rebrand, or fix a poorly-named device/entity.
---

# HA Rename

Fix poorly-named devices/entities on the live HA instance. There's no staging environment, so consumers must be found and patched deliberately — a rename that breaks an automation or dashboard has no safety net.

## Steps

1. Resolve the input via `ha_search`/`ha_get_entity`/`ha_get_device` to confirm what it currently is: domain, area, device, current friendly name and `entity_id`.
2. Consult the `home-assistant-best-practices` skill for the reference file on renaming entities/migrating device_id to entity_id, per `CLAUDE.md`'s Process section — read it before proposing or applying anything.
3. Search for every consumer of this entity_id (and the device, if a device was given) per `CLAUDE.md`'s "check consumers before touching" rule:
   - Automations (triggers, conditions, actions)
   - Scripts
   - Scenes
   - Dashboards/Lovelace cards
   - Other entities/templates that reference it (e.g. template sensors, groups)

   Use `ha_search` plus `ha_config_get_automation`/`ha_config_get_script`/`ha_config_get_scene`/`ha_config_get_dashboard` as needed. List every consumer found, even if the rename later turns out trivial.
4. Propose a new `entity_id` (and friendly name, if it also needs fixing) following `CONVENTIONS.md`'s Naming Convention and Common Entity Patterns sections. Briefly explain why the current name is poor and why the new one fits.
5. Present the proposed name plus the full consumer list to the user and ask for approval before changing anything. If the user wants a different name, iterate until approved.
6. Once approved:
   - Rename the entity (and update the friendly name/icon if applicable) via `ha_set_entity`.
   - Patch every consumer found in step 3 to use the new entity_id (automations via `ha_config_set_automation`, scripts via `ha_config_set_script`, scenes via `ha_config_set_scene`, dashboards via `ha_config_set_dashboard`).
   - If any consumer can't be safely auto-patched (e.g. an ambiguous template reference), don't guess — add it to `ISSUES.md` under `TBD` instead.
7. Update `STATE.md` to reflect the new entity_id/name wherever it's listed.
8. Append an entry to `CHANGELOG.md` describing the rename and which consumers were patched, per `CLAUDE.md`'s changelog format, then commit (live-state change + STATE.md + CHANGELOG.md together, per the existing commit convention).
9. Ask if the user wants to rename another device/entity.

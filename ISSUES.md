# Issues

Observations flagged while working on something else — not acted on unless asked. Every entry starts as `TBD`; update the status once reviewed (e.g. `Fixed`, `Won't fix`, `Ignored`) rather than deleting the line, so there's a record.

## TBD

- (2026-06-24, `/state`) `Routine: Bedtime Start` was listed in `STATE.md` as an existing automation but doesn't exist live — only `automation.bedtime_routine_turn_off` ("Routine: Bedtime") was found. Confirm whether it was removed, renamed, or merged, and whether anything still expects it to exist.
- (2026-06-24, `/state`) Three automations use a `Holiday:` prefix (`Holiday: Front Door Opens - Lights On`, `Holiday: Door Opened Timer Finished - Lights Off`, `Holiday: Front Door Opened Timer`) that isn't one of the 7 categories in `CLAUDE.md`'s Naming Convention. Decide whether to rename them into an existing category or formalize `Holiday` as an 8th category.
- (2026-06-24, `/state`) `Garage override: On` / `Garage override: Off` use the prefix "Garage override:" rather than "Garage:", inconsistent with sibling Garage automations.
- (2026-06-24, `/state`) `STATE.md`'s Sensors > Temperature & Illuminance lists a "Pool area" sensor, but no pool-related entity was found via `ha_search`. Confirm whether this is stale or just not discoverable by that search term.
- (2026-06-24, `/state`) `STATE.md`'s Key Devices "Main TV" entity_id `media_player.living_room_tv_2` no longer exists. Live candidates are `media_player.living_room_tv_2_2` ("Living Room Sony TV", state `unavailable`) and `media_player.living_room` ("Living Room", state `off`). Confirm which is the real main TV and check for any automations/scripts/dashboards still referencing the old entity_id.

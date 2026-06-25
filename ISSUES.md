# Issues

Observations flagged while working on something else — not acted on unless asked. Every entry starts as `TBD`. Once fixed, delete the line entirely — git history and `CHANGELOG.md`/`DOCS-CHANGELOG.md` already record what happened. If resolved as `Won't fix` or `Ignored` (no actual change made), update the line in place with that status prefix instead, since there's no other record of the decision.

## TBD

- (2026-06-24, `/conventions`) `binary_sensor.hue_outdoor_motion_sensor_1_motion` ("Backyard Sensor Motion") and `binary_sensor.hue_outdoor_motion_sensor_1_motion_2` ("Front Yard sensor Motion") — device-based ids instead of area-based, with a `_2` collision suffix.
- (2026-06-24, `/conventions`) `sensor.hue_outdoor_motion_sensor_1_temperature`/`_temperature_2` and `_illuminance`/`_illuminance_2` — same device-based naming issue as the binary_sensor pair above (Backyard/Front Yard).
- (2026-06-24, `/conventions`) `automation.front_door_motion_lights` ("Front Door: Motion Lights") — category prefix doesn't match its assigned area (`entry`). Worth a look.
- (2026-06-24, `/conventions`) `automation.tag_man_cave_is_scanned` ("Man cave: Scanned") — lowercase "cave" inconsistent with "Man Cave" capitalization used elsewhere. Worth a look.
- (2026-06-24, `/conventions`) `light.tv_lightstrip` — no area prefix (likely Living Room). Worth a look.
- (2026-06-24, `/conventions`) `light.outdoor`, `light.outdoor_2`–`light.outdoor_5`, `light.outdoor_porch` — generic "outdoor" naming, ambiguous which physical area each belongs to. Worth a look.
- (2026-06-24, `/conventions`) `binary_sensor.outdoor_sensor_motion`, `sensor.outdoor_sensor_temperature`, `sensor.outdoor_sensor_illuminance` — id says "outdoor" but friendly name/area is "Entry Porch". Worth a look (historical naming).
- (2026-06-24, `/conventions`) `binary_sensor.hallway_motion` vs `binary_sensor.hallway_sensor_1_motion` (plus matching temperature/illuminance sensors) — two inconsistently-named hallway sensors; possibly one is redundant/legacy. Worth a look.
- (2026-06-25, `/issues`) While patching consumers for the coffee_machine/dehumidifier rename, noticed `Mode: Holiday On` (`automation.holiday_mode_enable_from_notification`) and `Routine: Bedtime` (`automation.bedtime_routine_turn_off`) use template conditions/variables (`is_state()`, `{% if %}`) for door/garage state checks where native `state` conditions would work — flagged by the best-practices skill as anti-patterns. Worth a refactor pass.

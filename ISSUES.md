# Issues

Observations flagged while working on something else — not acted on unless asked. Every entry starts as `TBD`. Once fixed, delete the line entirely — git history and `CHANGELOG.md`/`DOCS-CHANGELOG.md` already record what happened. If resolved as `Won't fix` or `Ignored` (no actual change made), update the line in place with that status prefix instead, since there's no other record of the decision.

## TBD

- (2026-06-25, `/issues`) The `front_yard` area's `temperature_entity_id` config field still points at the old entity_id `sensor.hue_outdoor_motion_sensor_1_temperature_2` (now renamed to `sensor.front_yard_temperature`) — the available MCP tools don't expose a way to update this area field directly. Needs a manual fix via Settings → Areas → Front Yard → Temperature sensor (or a future tool addition), otherwise the area card will show no/stale temperature.
- (2026-06-25, `/issues`) While patching consumers for the coffee_machine/dehumidifier rename, noticed `Mode: Holiday On` (`automation.holiday_mode_enable_from_notification`) and `Routine: Bedtime` (`automation.bedtime_routine_turn_off`) use template conditions/variables (`is_state()`, `{% if %}`) for door/garage state checks where native `state` conditions would work — flagged by the best-practices skill as anti-patterns. Worth a refactor pass.

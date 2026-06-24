# Changelog

Log of changes made to this Home Assistant instance.

## 2026-06-24

1. Rewired 5 holiday-mode automations (`Alert: Holiday Light On`, `Routine: Holiday Lights`, `Routine: Holiday Lights Off`, `Holiday: Front Door Opens - Lights On`, `Holiday: Door Opened Timer Finished - Lights Off`) to target individual lights instead of Hue Bridge groups; excluded `light.coffee_machine` and `light.kitchen_light_bar` from the kitchen/dining "lights on" triggers since those have dedicated automations.
2. Disabled all 16 known Hue group entities (`light.kitchen`, `light.living_room`, `light.living_room_2`, `light.dining_room`, `light.bathroom`, `light.hallway`, `light.man_cave`, `light.entry`, `light.study`, `light.toilet`, `light.garage`, `light.zone_hallway`, `light.zone_kitchen`, `light.zone_living_area`, `light.backyard`, `light.front_yard`).

## 2026-06-25

1. Fixed a regression from the 2026-06-24 Hue-group disable: that pass only re-audited the 5 holiday-mode automations, missing 4 others that also referenced the now-disabled groups. Swept all 67 automations directly via config and found: `Hallway: Motion` (`automation.hallway_sensor`) still targeted `light.hallway` — retargeted to `light.hallway_downlight_1`/`light.hallway_downlight_2`. Verified live: user confirmed hallway lights turn on with motion.
2. `Mode: Holiday Off` (`automation.mode_holiday_off`) still targeted `light.living_room` for the welcome-home lights — retargeted to `light.living_room_main`/`light.living_room_lamp`. Verified live: toggling `input_boolean.holiday_mode` on then off turned both lights on at brightness 76/255 (≈30%), matching `brightness_pct: 30`.
3. `Mode: Man Cave Off` (`automation.man_cave_toggle_off`) had conditions checking `light.backyard`/`light.kitchen` (disabled groups) before turning on backyard/kitchen lights post-dusk — replaced with state conditions against the real per-fixture entity lists (`light.backyard_floodlight`/`_pedestal_1`/`_pedestal_2`/`_spotlight_1`/`_spotlight_2`; `light.kitchen_downlight_1`/`_2`/`_3`/`_1_2`). Condition logic verified via template eval; full live test deferred to after dusk — logged in `VERIFY.md`.
4. `Presence: Arrival Lights` (`automation.presence_arrival_lights`) had conditions AND action target referencing `light.living_room`/`light.dining_room` (disabled groups) — replaced with `light.living_room_main`/`light.living_room_lamp`/`light.dining_room_main`. Untestable now (needs sunset + a real arrival/door-open event) — logged in `VERIFY.md`.

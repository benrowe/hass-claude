# Changelog

Log of changes made to Ben Rowe's Home Assistant instance.

## 2026-06-24

- Rewired 5 holiday-mode automations (`Alert: Holiday Light On`, `Routine: Holiday Lights`, `Routine: Holiday Lights Off`, `Holiday: Front Door Opens - Lights On`, `Holiday: Door Opened Timer Finished - Lights Off`) to target individual lights instead of Hue Bridge groups; excluded `light.coffee_machine` and `light.kitchen_light_bar` from the kitchen/dining "lights on" triggers since those have dedicated automations.
- Disabled all 16 known Hue group entities (`light.kitchen`, `light.living_room`, `light.living_room_2`, `light.dining_room`, `light.bathroom`, `light.hallway`, `light.man_cave`, `light.entry`, `light.study`, `light.toilet`, `light.garage`, `light.zone_hallway`, `light.zone_kitchen`, `light.zone_living_area`, `light.backyard`, `light.front_yard`).

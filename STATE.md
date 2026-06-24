# STATE.md - Home Assistant Instance Snapshot

Point-in-time inventory of this Home Assistant instance. Not auto-loaded into context — read this file only when you need a quick orientation, and never trust it over a live MCP query. Counts and lists here can drift the moment someone adds/renames/removes something through the UI; if you're about to act on an entity/automation, verify it exists via `ha_search` / `ha_get_state` / `ha_get_entity` first.

> **Maintenance:** After making changes to the HA setup (automations, helpers, integrations, areas, etc.), update this file to reflect the current state.

## Overview

- **Entities:** 554
- **Devices:** ~118
- **Integrations:** ~38
- **Areas:** 14
- **Automations:** 67

## Areas/Rooms

### House
- Living Room
- Kitchen
- Dining Room
- Bedroom
- Bathroom
- Toilet
- Study
- Entry
- Hallway
- Spare Room
- Backyard
- Man Cave

### Garage/External
- Garage
- Front Yard

### Disabled Hue Group Entities

All 16 known Hue group entities have been disabled (registry-level, `disabled_by: user`): `light.kitchen`, `light.living_room`, `light.living_room_2`, `light.dining_room`, `light.bathroom`, `light.hallway`, `light.man_cave`, `light.entry`, `light.study`, `light.toilet`, `light.garage`, `light.zone_hallway`, `light.zone_kitchen`, `light.zone_living_area`, `light.backyard`, `light.front_yard`. See "Hue Groups" in `GOTCHAS.md` for the policy. Automations that previously targeted these groups were rewired to target the individual member lights directly.

## Key Integrations

| Integration | Devices | Purpose |
|-------------|---------|---------|
| Philips Hue | 65 | Lighting (bulbs, light strips, motion sensors) |
| Google Cast | 10 | Media/speakers, TTS announcements |
| Unifi Protect | 5 | Security cameras, smart detection |
| IKEA Dirigera | 2 hubs | Door/window sensors, smart plugs (Man Cave hub: `01KHQ3FME5ERP02BKGF5M6KTHV`; Garage hub: `01KA77PTGFNGJ8PZWGQMSA2821`) |
| Sensibo | 3 | Climate control (AC units) |
| OpenGarage | 2 | Garage door control |
| Mobile App | - | Presence detection, notifications |
| Meater | - | Cooking probe |
| LIFX | - | Additional smart lighting |
| SolarEdge | - | Solar energy monitoring |
| Watchman | - | Automation entity/action validation |
| Google Calendar | - | Calendar integration |
| Spotify | - | Music playback control; entity `media_player.spotify_1123932196` |

## Automation Categories

### Room (28 automations)
Automations scoped to a single room, named by trigger:
- **Garage:** Open, Close, Override On/Off (push notifications → `/house-state`), Scanned, Auto disable override when doors close, Left Doors Open Too Long (push notification → `/house-state`), No Motion With Override Enabled (push notification → `/house-state`)
- **Man Cave:** Open, Scanned, Temperature Check (push notification with AC action buttons → `/house-state`), AC Prompt Response (push notification → `/house-state`), Sim Rig On, Sim Rig Off
- **Kitchen:** Motion, Light Bar On With No Motion, Light Bar Turned Off
- **Living Room:** TV On, TV Off
- **Dining Room:** Coffee Machine On, Coffee machine left on Reminder, Coffee Machine ready, Turn off coffee machine via Notification
- **Front Yard:** Motion Lights
- **Backyard:** Motion Lights, Motion: Turn on kitchen when man cave is on
- **Front Door:** Motion Lights
- **Hallway:** Motion Lights

> Note: `Garage override: On` / `Garage override: Off` use the prefix "Garage override:" rather than "Garage:" — flagged in ISSUES.md.

### Routine (8 automations)
Time-based or scheduled whole-house behaviors:
- `Routine: Morning` - Wake time triggers (lights, coffee machine, dehumidifier)
- `Routine: Dusk` - Sunset outdoor lights
- `Routine: Bedtime` - Nightly turn-off routine
- `Routine: Outdoor Lights Off` - 11 PM outdoor lights off
- `Routine: Holiday Lights` - Random light simulation for occupancy while away
- `Routine: Holiday Lights Off` - Turn off simulation lights at bedtime
- `Routine: Man Cave Dusk Adjust` - Adjust man cave lights to evening color temp at sunset
- `Routine: Update Available` (`automation.alert_update_available`) - Weekly digest of available HA updates (Monday 7am, or on leaving home on Monday)

> Note: `Routine: Bedtime Start` (previously listed here) no longer exists live — flagged in ISSUES.md.

### Mode (10 automations)
State toggles affecting multiple systems:
- `Mode: Driving On` / `Mode: Driving Off` - In-car mode with lighting changes
- `Mode: Movie On` - Cinema lighting scene
- `Mode: Entertainment On` - Dining entertainment scene
- `Mode: Entertainment Arm` (`automation.mode_entertainment_arm`) - Arms/prepares entertainment mode
- `Mode: Holiday On` - Vacation mode with security checks, garage closure, AC/coffee shutdown; push notifications (security warning, garage failure, confirmation) → `/house-state`
- `Mode: Holiday Off` - Welcome home scene with climate control; push notification → `/house-state`
- `Mode: Man Cave On` / `Mode: Man Cave Off` - Man cave active state toggle
- `Away Mode: AC Off Response` (`automation.away_mode_ac_off_response`) - Handles notification action response to turn off AC when away mode activates

### Presence (8 automations)
Triggered by people's location:
- `Presence: Everyone Left` - Away mode activation; push notification → `/house-state`
- `Presence: Someone Arrives` - Away mode deactivation; push notification → `/house-state`
- `Presence: Ben Arrives Android Auto` - Garage door auto-open; push notification → `/house-state`
- `Presence: Carol Leaves Castle Hill` - Push notification → `/map`
- `Presence: Carol at Driveway` - TTS announcement
- `Presence: Both at Sydney Airport` - Holiday mode prompt with action buttons → `/house-state`
- `Presence: Both at Changi Airport` - Holiday mode auto-enable
- `Presence: Arrival Lights` - Turn on lights when arriving home after dusk

### Alert (8 automations)
Notifications, warnings, reminders:
- `Alert: Doorbell Ring` - TTS + chime
- `Alert: Battery Low` - Low battery notifications (blueprint-managed)
- `Alert: Shopping List Reminder` - Shopping zone notification
- `Alert: Meater 20min Remaining` - Cooking timer TTS to Man Cave + Kitchen speakers
- `Alert: Meater Cook Started` - Notification when a Meater cook begins
- `Alert: Meater Estimated Time Available` - Notification once Meater estimates a finish time
- `Alert: Holiday Door Opened` - Push notification when doors open while on holiday → `/house-state`
- `Alert: Holiday Light On` - Push notification when lights turn on unexpectedly while on holiday → `/house-state`

> Note: Garage and coffee machine alerts were renamed to use room prefixes (Garage:/Dining Room:) and are now listed under Room automations.

> Note: Man Cave On/Off automations are in Mode category (`Mode: Man Cave On` / `Mode: Man Cave Off`). Room count reflects this.

> Note: All push notifications to `notify.mobile_app_ben_s_pixel_pro` include `clickAction: "/house-state"` unless otherwise noted above.

### System (2 automations)
HA internals:
- `System: Set Theme` - Startup theme configuration
- `System: IKEA Hub Auto-Restart` - Automatic restart of the IKEA Dirigera hub(s)

> Note: 3 further automations (`Holiday: Front Door Opens - Lights On`, `Holiday: Door Opened Timer Finished - Lights Off`, `Holiday: Front Door Opened Timer`) use a `Holiday:` prefix not in the 7-category list above — flagged in `ISSUES.md` rather than forced into a category here. Categorized total above (28+8+10+8+8+2=64) plus these 3 = 67 live automations.

## Helpers & Toggles

### Input Booleans (Modes)
- `input_boolean.holiday_mode` - Away/vacation mode
- `input_boolean.holiday_lights_active` - Tracks when holiday lights automation is controlling lights
- `input_boolean.using_garage_override` - Manual garage control
- `input_boolean.man_cave` - Man cave active state
- `input_boolean.driving_mode` - In-car mode
- `input_boolean.away_mode` - Away from home
- `input_boolean.bed_time` - Bedtime mode active
- `input_boolean.entertainment_mode` - Entertainment mode active
- `input_boolean.ben_s_ipad_in_use` - Tracks whether the iPad is in use

### Input Numbers
- Speaker volumes (per room/device)
- Outdoor brightness levels
- Temperature comfort thresholds

### Input Texts
- `input_text.announcement_state` - Serialised JSON of speaker state before TTS announcements (vol, playing, content_id, content_type). Written by `script.announce_speaker` for reliable volume/playback restore.

### Input DateTimes
- `input_datetime.wake_time` - Morning routine trigger (friendly name: "Time: Wake Time")
- `input_datetime.bed_time` - Bedtime routine trigger (friendly name: "Time: Bedtime")
- `input_datetime.bed_time_today` - Today's dynamic bed time override

## Sensors

### Motion Sensors (11 total)
- **Hue Motion Sensors:** Kitchen, Man Cave, Outdoor areas
- **IKEA:** `binary_sensor.sensor_1` → "Kitchen Motion Sensor" (IKEA Dirigera)
- **Camera-based:** Unifi Protect smart detection (driveway, front door)
- **Entry Porch:** `binary_sensor.outdoor_sensor_motion` → "Entry Porch Sensor Motion" (previously "Landing sensor")

### Door/Window Sensors
- Kitchen door sensor (IKEA)
- Front door sensor (IKEA)

### Temperature & Illuminance
- Kitchen
- Man Cave
- Outdoor areas
- Pool area

### Smart Detection (Unifi Cameras)
- Person detection
- Vehicle detection
- Animal detection
- Smoke detection
- CO detection

## Dashboards

| Title | URL Path | Purpose |
|-------|----------|---------|
| House | `/house-state` | Primary dashboard — used as `clickAction` in all mobile notifications |
| Map | `/map` | Person/device location tracking |

## Key Devices

- **Main TV:** unconfirmed — `media_player.living_room_tv_2` (previously listed here) no longer exists. Live candidates: `media_player.living_room_tv_2_2` ("Living Room Sony TV", state `unavailable`) and `media_player.living_room` ("Living Room", state `off`). See `ISSUES.md`.
- **Stale/disabled:** `media_player.living_room_tv` — also no longer found.

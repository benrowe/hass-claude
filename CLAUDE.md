# CLAUDE.md - Home Assistant Setup

This file provides context for Claude Code when working with Ben Rowe's Home Assistant instance.

> **Maintenance:** After making changes to the HA setup (automations, helpers, integrations, areas, etc.), update this file to reflect the current state.

## Overview

- **Entities:** 482
- **Devices:** ~118
- **Integrations:** ~38
- **Areas:** 14
- **Automations:** 58

### Goals
- Convenience & comfort
- Energy efficiency
- Security & safety

### Naming Convention

**Format:** `{Category}: {Trigger Description}`

- **Entities:** Room/Area/Zone based (e.g., `light.kitchen_downlights`, `cover.garage_door_car`)
- **Automations:** Category prefix + trigger-based name. Actions may change over time; trigger-based names remain stable.

#### Automation Categories (7 total)

| Category | Prefix | Purpose |
|----------|--------|---------|
| **Room** | `{Room}:` | Automations triggered by and primarily affecting a single room |
| **Routine** | `Routine:` | Time-based or scheduled whole-house behaviors |
| **Mode** | `Mode:` | State toggles that affect multiple systems |
| **Presence** | `Presence:` | Triggered by people arriving/leaving/detected |
| **Alert** | `Alert:` | Notifications, warnings, reminders (informational only) |
| **Device** | `Device:` | Device-specific logic not tied to a room |
| **System** | `System:` | HA internals, startup, integrations |

#### Decision Rules

1. **Single room scope** → Use Room prefix (e.g., `Kitchen: Motion`, `Garage: Door Opens`)
2. **Time/schedule based** → Use Routine prefix (e.g., `Routine: Morning`, `Routine: Dusk`)
3. **Changes house-wide behavior** → Use Mode prefix (e.g., `Mode: Driving On`, `Mode: Movie On`)
4. **Person location trigger** → Use Presence prefix (e.g., `Presence: Everyone Left`, `Presence: Carol at Driveway`)
5. **Informational output only** → Use Alert prefix (e.g., `Alert: Doorbell Ring`, `Alert: Battery Low`)
6. **Device-specific, not room-tied** → Use Device prefix
7. **HA internals** → Use System prefix

### Icon Guidelines

All automations, helpers, and entities should have appropriate MDI icons. Icons are set via the entity registry using `ha_set_entity()`.

**Key Principle:** Icons should describe **what the automation does**, not what triggers it.

| Category | Icon Style | Examples |
|----------|------------|----------|
| **Mode On** | Action/destination icon | `mdi:airplane` (holiday), `mdi:car` (driving), `mdi:movie` (movie mode) |
| **Mode Off** | Welcome/return icon | `mdi:home-heart` (welcome home) |
| **Routine (lights)** | Light-related action | `mdi:home-lightbulb` (simulate occupancy), `mdi:lightbulb-off` (turn off) |
| **Alert (security)** | Shield/warning icons | `mdi:shield-alert` (intrusion), `mdi:bell-alert` (unexpected activity) |
| **Alert (info)** | Topic-specific | `mdi:coffee` (coffee ready), `mdi:battery-alert` (low battery) |
| **Presence** | Person/location icons | `mdi:home-account` (arrival), `mdi:exit-run` (departure) |

**Common MDI Icons:**
- Security: `mdi:shield-alert`, `mdi:shield-home`, `mdi:shield-lock`
- Lights: `mdi:lightbulb`, `mdi:lightbulb-off`, `mdi:home-lightbulb`, `mdi:lightbulb-auto`
- Notifications: `mdi:bell-alert`, `mdi:message-alert`, `mdi:alert-circle`
- Home: `mdi:home-heart`, `mdi:home-account`, `mdi:home-automation`
- Climate: `mdi:thermometer`, `mdi:air-conditioner`, `mdi:snowflake`
- Travel: `mdi:airplane`, `mdi:car`, `mdi:palm-tree`

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

### Garage/External
- Garage
- Front Yard
- Man Cave

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

### Room (24 automations)
Automations scoped to a single room, named by trigger:
- **Garage:** Open, Close, Override On/Off (push notifications → `/house-state`), Scanned, Auto disable override when doors close, Left Doors Open Too Long (push notification → `/house-state`), No Motion With Override Enabled (push notification → `/house-state`)
- **Man Cave:** Open, Scanned, Temperature Check (push notification with AC action buttons → `/house-state`), AC Prompt Response (push notification → `/house-state`)
- **Kitchen:** Motion
- **Living Room:** TV On, TV Off
- **Dining Room:** Coffee Machine On, Coffee machine left on Reminder, Coffee Machine ready, Turn off coffee machine via Notification
- **Front Yard:** Motion Lights
- **Backyard:** Motion Lights, Motion: Turn on kitchen when man cave is on
- **Front Door:** Motion Lights
- **Hallway:** Motion Lights

### Routine (9 automations)
Time-based or scheduled whole-house behaviors:
- `Routine: Morning` - Wake time triggers (lights, coffee machine, dehumidifier)
- `Routine: Dusk` - Sunset outdoor lights
- `Routine: Bedtime` - Nightly turn-off routine
- `Routine: Bedtime Start` - Bedtime boolean trigger
- `Routine: Outdoor Lights Off` - 11 PM outdoor lights off
- `Routine: Holiday Lights` - Random light simulation for occupancy while away
- `Routine: Holiday Lights Off` - Turn off simulation lights at bedtime
- `Routine: Man Cave Dusk Adjust` - Adjust man cave lights to evening color temp at sunset
- `Routine: Update Available` (`automation.alert_update_available`) - Weekly digest of available HA updates (Monday 7am, or on leaving home on Monday)

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

### Alert (6 automations)
Notifications, warnings, reminders:
- `Alert: Doorbell Ring` - TTS + chime
- `Alert: Battery Low` - Low battery notifications (blueprint-managed)
- `Alert: Shopping List Reminder` - Shopping zone notification
- `Alert: Meater 20min Remaining` - Cooking timer TTS to Man Cave + Kitchen speakers
- `Alert: Holiday Door Opened` - Push notification when doors open while on holiday → `/house-state`
- `Alert: Holiday Light On` - Push notification when lights turn on unexpectedly while on holiday → `/house-state`

> Note: Garage and coffee machine alerts were renamed to use room prefixes (Garage:/Dining Room:) and are now listed under Room automations.

> Note: Man Cave On/Off automations are in Mode category (`Mode: Man Cave On` / `Mode: Man Cave Off`). Room count reflects this.

> Note: All push notifications to `notify.mobile_app_ben_s_pixel_pro` include `clickAction: "/house-state"` unless otherwise noted above.

### System (1 automation)
HA internals:
- `System: Set Theme` - Startup theme configuration

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

## Key Automation Patterns

### Manual Override Detection
Kitchen uses a light bar as manual override indicator - if the light bar is on, motion automations are suppressed.

### NFC Tag Triggers
- Garage: Quick access control
- Man Cave: Toggle on/off

### Motion + Condition Checks
Motion sensors trigger lights only when:
- Illuminance is below threshold
- Time is within allowed window
- Override modes are not active

### TTS Announcements
- All TTS announcements use `script.announce_speaker` (reusable script)
- Captures volume + playback state before announcing, restores after
- Supports optional chime before TTS, and optional `spotify_entity` for Spotify resume
- **Important:** Google Cast TTS (Default Media Receiver) tears down any active Spotify Cast session. Resuming Spotify requires calling `media_player.select_source` + `media_player.media_play` on the Spotify integration entity (`media_player.spotify_1123932196`), not on the Cast entity directly.

### Scripts
- `script.announce_speaker` — Reusable TTS announcement with chime, volume management, and playback resume. Fields: `media_players` (required), `message` (required), `volume` (default 0.8), `chime` (optional media-source URL), `state_helper` (optional input_text for state copy), `spotify_entity` (optional Spotify entity for reliable resume). Call via `script.turn_on` with `variables:` to pass fields reliably.

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

- **Main TV:** `media_player.living_room_tv_2` (Living Room TV - Chromecast)
- **Stale/disabled:** `media_player.living_room_tv` — disabled (duplicate, do not use)

## Common Entity Patterns

### Lights
- `light.<area>_<fixture>` (e.g., `light.kitchen_downlights`)
- `light.<area>_light_bar` for override indicators

### Climate
- `climate.<area>_ac` (Sensibo units)

### Covers
- `cover.garage_door_<n>` (OpenGarage)

### Sensors
- `binary_sensor.<area>_motion` (motion detection)
- `sensor.<area>_temperature` (temperature readings)
- `sensor.<area>_illuminance` (light levels)

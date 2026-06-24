# CONVENTIONS.md - Naming & Style Conventions

Opinionated naming/style conventions for this Home Assistant instance — entities, devices, automations, icons. These are choices, not technical constraints; a different setup could reasonably make different calls. For hard technical constraints/quirks (integration bugs, surprising HA behavior), see `GOTCHAS.md` instead. For process/policy (when to consult skills, how to log changes), see `CLAUDE.md`.

> **Maintenance:** Update this file when a naming/icon convention changes. Audit the live instance against it with `/conventions`.

## Naming Convention

**Format:** `{Category}: {Trigger Description}`

- **Entities:** Room/Area/Zone based (e.g., `light.kitchen_downlights`, `cover.garage_door_car`)
- **Devices:** `{Area} {Fixture}` in title case (e.g., "Kitchen Downlights", "Garage Door Car") — mirrors the entity_id so it can be mechanically derived (lowercase, spaces → underscores). Replace vendor-default device names (e.g. "Hue color lamp 3", "Smart Plug") with this pattern.
- **Automations:** Category prefix + trigger-based name. Actions may change over time; trigger-based names remain stable.
- **Exceptions:** Entities/devices that aren't area-bound (e.g. `person.*`, `zone.*`, `update.*`, integration/hub diagnostic entities) don't need to fit the area-based pattern — name them for what they represent instead (e.g. `person.ben`, `update.hue_bridge_firmware`).

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

## Icon Guidelines

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
- `sensor.<area>_illuminance` (light level readings)

### Switches & Plugs
- `switch.<area>_<fixture>` (e.g., `switch.man_cave_sim_rig`)

### Locks
- `lock.<area>_<fixture>` (e.g., `lock.front_door`)

### Media Players
- `media_player.<area>_<fixture>` (e.g., `media_player.living_room_tv`)

### Fans
- `fan.<area>_<fixture>` (e.g., `fan.bedroom_ceiling`)

### Buttons
- `button.<area>_<fixture>` or `button.<device>_<action>` for NFC/scene-trigger buttons (e.g., `button.garage_nfc_tag`)

### Vacuums
- `vacuum.<area>_<fixture>` if area-bound, otherwise `vacuum.<device_name>` (e.g., `vacuum.robot`)

### Cameras
- `camera.<area>_<fixture>` (e.g., `camera.driveway`)

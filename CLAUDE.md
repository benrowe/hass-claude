# CLAUDE.md - Home Assistant Setup

This file provides context for Claude Code when working with Ben Rowe's Home Assistant instance. It holds durable conventions and policies — not a snapshot of current entities/automations/helpers, which would go stale and mislead. For a point-in-time inventory (entity/device counts, areas, integration device counts, full automation list, helpers, sensors, dashboards, key devices), see `STATE.md` — read it only when you need orientation, and never trust it over a live MCP query (`ha_search`, `ha_get_state`, `ha_get_entity`, etc.) for anything you're about to act on.

> **Maintenance:** After making changes to the HA setup (automations, helpers, integrations, areas, etc.), update `STATE.md` to reflect the current state. Update this file (`CLAUDE.md`) only when a convention, policy, or pattern changes.

## Process

- **Before any persistent config change** (automations, scripts, scenes, helpers, dashboards, entities, areas, etc.), consult the `home-assistant-best-practices` skill ("Get Home Assistant Best Practices Skill Guide") and read the reference file(s) it points to for the task at hand.
- **Before touching any entity or automation** — not just renames/disables — search for consumers/usages first (automations, scripts, scenes, dashboards). This is a single live HA instance with no staging environment, so there's no safety net if a change breaks something downstream.
- **No domain needs extra confirmation by default** (locks, alarm, cameras included) — treat all entities the same unless told otherwise for a specific case.
- **If you notice an inconsistency, stale entity, or improvement opportunity while working on something else**, don't act on it — add it to `ISSUES.md` under a `TBD` entry instead. Only fix it if asked.
- **When you have clarifying questions, ask them one at a time** rather than bundling several into a single multi-question prompt — easier to give granular feedback on one question before moving to the next.
- **After any change that mutates server state** (the live HA instance — automations, helpers, entities, etc.), append an entry to `CHANGELOG.md` in the repo root.
- **After any change to this repo's own files/structure** (CLAUDE.md, STATE.md, README.md, conventions — not the live HA instance), append an entry to `DOCS-CHANGELOG.md` instead.
- Both changelogs use the same format:

  ```
  ## {year}-{month}-{day}

  1. {description}
  ```

  Use an ordered (numbered) list, not bullets — entries within a day's section represent logical steps in sequence. If today's date already has a section, append a new numbered item under it (continuing the numbering) rather than creating a new section. Each changelog is chronological — new dates are added at the bottom.
- **After updating either changelog**, commit the change immediately (e.g. `git add CHANGELOG.md && git commit -m "..."`). Each mutation gets its own commit — keep history granular, don't batch multiple changes into one commit.
- **After adding a new Claude Code slash command** (`.claude/commands/*.md`), add it to the "Available Commands" list in `README.md`.

## Goals
- Convenience & comfort
- Energy efficiency
- Security & safety

## Household Context

- **Cleaners** visit fortnightly while Ben/Carol are usually home — no automation impact.
- **Pet sitters** visit while `input_boolean.holiday_mode` is on (away). This is why holiday mode has front-door-triggered light automations (to assist the sitter) and alerts for doors opening / lights turning on unexpectedly — see `Holiday:`/`Alert: Holiday *` automations in `STATE.md`.

## Naming Convention

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

## Hue Groups

**Do not use Hue groups (Bridge-side room/zone groups).** They cause sync/state issues with HA. If a Hue group entity is found, disable it rather than using it for automations. See `STATE.md` for the current list of disabled groups.

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

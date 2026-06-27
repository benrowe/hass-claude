# Ideas

Speculative, unscheduled ideas — bigger or more open-ended than an `ISSUES.md` entry. Not acted on unless asked.

## True offline unit tests for automations

Right now `ha-verify` tests automations against the live instance — there's no staging environment, so "testing" means careful live verification, not isolated unit tests.

A real unit-test harness would need:
- Automations exported to YAML fixtures (they currently live only in the live instance's UI/registry, not in this repo).
- [`pytest-homeassistant-custom-component`](https://github.com/MatthewFlamm/pytest-homeassistant-custom-component) to spin up an in-memory HA core instance, load those YAML fixtures, fire triggers/set states, and assert resulting state — all without touching the real house.
- Some sync mechanism to keep exported YAML fixtures from drifting out of sync with the live registry (or to decide automations should be authored as YAML + deployed, rather than edited live).

Worth revisiting if this setup's automation count/complexity grows enough that live-only verification feels risky, or if this tooling (this repo + the `ha-verify`/`/rename`/`/conventions` skills) gets generalized and open-sourced for others to use on their own HA instances — at that point a real test suite would matter a lot more than it does for a single household's live config.

research: `pytest-homeassistant-custom-component`

## Connect Arlec devices via localtuya

Have a few Arlec smart devices (rebranded Tuya hardware) not yet connected to Home Assistant. Want them integrated so automations can be built around them.

- [`localtuya`](https://xzetsubou.github.io/hass-localtuya/) — HACS custom integration for local (LAN-only, no cloud dependency) control of Tuya-protocol devices. Looks like the right fit since Arlec devices speak Tuya's local protocol under the rebrand.
- Setup will need each device's Tuya `local_key` and `device_id`, typically pulled via the Tuya IoT Platform/Cloud API per-device, plus the device's local IP — see the linked docs for the extraction steps.
- Once paired, treat resulting entities like any other — apply naming/icon conventions from `CONVENTIONS.md` before building automations on top.

research: `localtuya` HACS integration, Tuya IoT Platform device key extraction

## Auto-Off Internal Lights on Departure

When both residents leave home, internal lights are often left on. This automation would trigger whenever either person leaves, check if both are now "not_home", wait 5 minutes, confirm both are still away, then turn off all internal lights.

- Trigger: `person.ben` or `person.carol` transitions to "not_home"
- Condition: both persons are "not_home" at trigger time
- Condition: `input_boolean.holiday_mode` is off (pet sitter may need lights)
- Action: wait 5 minutes, re-check both still away, call `light.turn_off` on all internal light groups/areas
- Automation mode: `restart` — so if the second person leaves, comes back, then leaves again, the 5-minute timer resets cleanly
- Need to identify the right light targets — likely a `light` domain call scoped to internal areas only

research: whether HA supports a single "all indoor lights" group or if targets need to be enumerated per area

## Auto-open spare garage on motorcycle arrival

When Ben arrives home on the motorcycle, the spare garage door should open automatically — mirroring the `Presence: Ben Arrives Android Auto` pattern but using Bluetooth headset connection as the "riding" signal instead of Android Auto.

The phone exposes `sensor.ben_s_pixel_pro_bluetooth_connection` with a `connected_paired_devices` attribute listing currently-connected devices by MAC + name. The motorcycle headset is `PT EDGE` (`00:0A:9B:D1:BC:22`) — its presence in that list is a reliable proxy for being on the bike.

- Trigger: `person.ben` enters `zone.home`
- Condition: `00:0A:9B:D1:BC:22 (PT EDGE)` is in the `connected_paired_devices` attribute of `sensor.ben_s_pixel_pro_bluetooth_connection`
- Action: `cover.open_cover` on `cover.garage_door_spare`, plus a push notification to `notify.mobile_app_ben_s_pixel_pro` confirming the door opened
- The attribute check can't use a native `condition: state` — best implemented as a Template Helper (binary sensor: `on` when PT EDGE is in the list), then the automation uses a native state condition on the helper
- No arrival delay needed (open immediately on zone entry, matching the car pattern)

research: whether the Template Helper approach is reliable when the Bluetooth sensor updates on headset connection/disconnection

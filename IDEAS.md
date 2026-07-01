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

## Kitchen Lights Auto-Off After 1 Hour No-Motion

When someone manually turns on the kitchen lights, the light bar comes on as a manual override indicator — which suppresses the normal motion-off automations. This means if everyone leaves the kitchen without manually turning the lights off, they stay on indefinitely. This idea adds a safety-net automation: if the kitchen motion sensor has seen no motion for over an hour **and** the light bar is on, turn off all lights in the kitchen area (including the light bar, which clears the override state).

- Trigger: kitchen motion sensor transitions to idle, or a periodic time-based check
- Condition: motion sensor last changed > 60 minutes ago AND light bar is on
- Action: turn off all lights in the kitchen area (lights + light bar)
- Note: turning off the light bar clears the manual override, so if someone re-enters the kitchen after this fires, motion automations will resume normally

## Context-Aware Garage Door Left-Open Notification

When the "garage door left open" alert fires, the notification method should adapt based on who is currently home (using `person` entity states):

- **No one home** — push notification to both people
- **One person home** — push notification to that person + TTS announcement on the kitchen speaker
- **Both people home** — TTS on the kitchen speaker only (no push)

The motivation is that a speaker announcement is more natural and less disruptive when someone is physically present; when both people are home, a single speaker call is sufficient without doubling up on phone pings.

Key steps:

- Locate the existing garage door left-open automation and identify where the notify action lives
- Replace the action with a `choose` block that branches on `person` entity states
- Per-person push: use the existing per-person notify service targets
- TTS: call `script.announce_speaker` targeting the kitchen media player
- Holiday mode (`input_boolean.holiday_mode`) implies no one is home — "no one home" branch should also apply there

research: Review the current garage door open notification automation to understand the existing action structure before modifying it

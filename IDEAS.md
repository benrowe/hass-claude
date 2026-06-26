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

## Auto-close garage on car departure

When a household member leaves the home zone while connected to Android Auto, HA should check if the garage door is open and close it automatically, then notify the person who just left that it was closed on their behalf. This handles the common "drove off and forgot the garage" scenario without needing to check manually.

The trigger mirrors the pattern already used in the arrival automation: person's zone changes from `home` while their Android Auto sensor is `on`. Because both household members drive, this needs to handle either person departing — with the notification going to the same phone that triggered it (not a broadcast). Two automations (one per person) is the simplest approach since the notify target is person-specific; a single automation using `trigger.entity_id` to resolve the right notify service is an alternative.

- Identify the Android Auto sensor entity_ids for both people (already used in the arrival automation — check there).
- Identify the garage door cover entity (`cover.garage_door_car` or similar) and confirm the close service works reliably from automation context.
- Confirm the per-person notify service names (`notify.mobile_app_<device>`).
- Decide: two automations vs. one with per-trigger routing (`choose` on `trigger.entity_id`).
- Consider a brief delay (5–10 s) after zone departure before checking the door — zone exit can fire before the car has physically cleared the garage.

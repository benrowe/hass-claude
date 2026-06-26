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

---
name: ha-verify
description: Facilitate live testing/verification of Home Assistant automations, scripts, scenes, and entities by proposing acceptance criteria, setting up baseline state via service calls, asking the user to perform any real-world trigger, then asserting the outcome via live MCP queries (ha_get_state/ha_get_history/ha_get_automation_traces). Use this every time after making a persistent config change on the live HA instance (automation, script, scene, helper, entity edit) — verification happens before logging to CHANGELOG.md. Also use it standalone when the user wants to debug or diagnose why something isn't behaving as expected, with no config change involved — the test-setup-trigger-assert loop is identical either way. Trigger on phrases like "test this", "verify it works", "check if X happens", "why isn't X triggering", "debug this automation".
---

# HA Verify

A repeatable loop for confirming HA behavior against reality instead of just trusting the config. There's no staging environment — this is the only safety net between "I changed the YAML/registry" and "I know it actually does the right thing."

This skill is the same whether you just changed something or you're debugging a pre-existing problem the user reported. The only difference is what happens after a passing test (see "After the test" below).

## The loop

1. **Propose a test plan.** Write concrete acceptance criteria: which entities should end up in which state/attribute values, under what preconditions, within what time window. Be specific enough that "pass" or "fail" is unambiguous (e.g. "`light.kitchen_downlights` reaches `brightness: 76` within 5s of motion, only if `light.kitchen_light_bar` is off and time is between 22:00–06:00"). Share this with the user before running anything.

2. **Set up baseline state.** Use service calls to put the system in the preconditions the test needs (e.g. turn off an override switch, reset a helper, set a `input_boolean` to the state the test assumes). This avoids a false pass/fail caused by leftover state from earlier testing.

3. **Trigger it.** Distinguish two kinds of trigger:
   - **Pure HA-internal state** (helper toggles, input_number changes, time_pattern, calling a script/automation's action directly) — do this yourself via `ha_call_service`. It's not a "real-world" event, so there's no need to involve the user.
   - **Real-world triggers** (motion, NFC tap, physical button press, a door actually opening, time-of-day waits) — ask the user to perform the action. Don't simulate these by firing the underlying entity's state via a service call unless the user explicitly says that's an acceptable substitute — faking the input defeats the point of testing the real trigger path (e.g. the actual motion sensor device, not just the binary_sensor entity it reports through).

4. **Assert the outcome.** Once the user confirms the action happened (or once you've fired it yourself), query live state — `ha_get_state`, `ha_get_history`, `ha_get_automation_traces` — and compare against the acceptance criteria from step 1. Report pass/fail plainly, citing the actual values observed.

5. **On failure: fix in place, then retest.** Diagnose using `ha_get_automation_traces`/`ha_get_logs`, adjust the config, and re-run the same test plan from step 2 (re-establish baseline first — don't assume it's still clean). Don't roll back the change automatically; the user can decide to abandon it if iteration isn't converging.

## When the test is time-sensitive and can't run now

Some acceptance criteria depend on a real-world condition that isn't true right now — sun position (`sun.sun` before/after sunset), someone being away/home, a season, a specific physical trigger you can't safely fake. If step 1-4 can't complete because of this, don't just drop it:

1. Verify whatever *is* checkable now — e.g. evaluate the condition logic against current state via `ha_eval_template`, or confirm the action's target entities are real and valid — so you have partial confidence even without a live run.
2. Log an entry to `VERIFY.md` under `Pending`: what was changed, what's already been checked, exactly why the live test is blocked, and the acceptance criteria for when it becomes testable again.
3. Tell the user it's logged rather than silently leaving it unverified.

When `/verify` is invoked with no argument, work through `VERIFY.md`'s `Pending` entries instead of asking what to test. When one passes, move it to `Verified` with the date and outcome.

## After the test

- **If this followed a persistent config change**, only proceed to the `CHANGELOG.md` entry + commit (per `CLAUDE.md`'s Process section) once the test passes. The changelog should only ever describe verified behavior.
- **If this was standalone debugging** (no preceding config change), there's no changelog step — just report the diagnosis. If the fix requires a config change, that change is itself now subject to this same loop and to `CLAUDE.md`'s "before any persistent config change" step (consult `home-assistant-best-practices`, check consumers, etc.).

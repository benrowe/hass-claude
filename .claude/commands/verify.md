---
description: Run the ha-verify testing loop against a recent change or a reported problem
---

Run the `ha-verify` skill against $ARGUMENTS (e.g. an automation/script/scene/entity you just changed, or a behavior the user says isn't working). Follow the skill's loop exactly: propose acceptance criteria, set up baseline state, trigger (yourself for HA-internal state, the user for real-world events), assert via live MCP queries, report pass/fail, and on failure fix-and-retest in place.

If no argument is given, read `VERIFY.md` and work through its `Pending` entries instead of asking what to verify. For each entry, check whether the condition that blocked it (time of day, presence, etc.) now holds — if so, run the test plan in its acceptance criteria; if not, leave it pending and move on to the next entry. Move passing entries to `Verified` with the date and outcome.

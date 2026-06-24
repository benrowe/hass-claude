---
description: Reset instance-specific docs (STATE/CHANGELOG/DOCS-CHANGELOG/ISSUES/IDEAS/VERIFY) for a fresh fork of this repo
---

Reset the six files that log/snapshot facts about *this specific* Home Assistant instance, so a fresh fork of this repo starts clean instead of inheriting someone else's history. `CLAUDE.md`, `README.md`, `.claude/commands/`, and `.claude/skills/` are NOT touched — they're generic conventions/tooling, reusable as-is.

## Steps

1. **Confirm with the user before proceeding.** This is destructive (it discards all entries in the six files below) and only makes sense once — if it's accidentally run on the original repo rather than a fork, all that history is lost. Ask explicitly: "This will reset STATE.md, CHANGELOG.md, DOCS-CHANGELOG.md, ISSUES.md, IDEAS.md, and VERIFY.md to empty skeletons, discarding all entries. Continue?"

2. **Overwrite each file with its skeleton** (keep the explanatory header/intro text, drop all dated entries/content):

   **STATE.md:**
   ```markdown
   # STATE.md - Home Assistant Instance Snapshot

   Point-in-time inventory of this Home Assistant instance. Not auto-loaded into context — read this file only when you need a quick orientation, and never trust it over a live MCP query. Counts and lists here can drift the moment someone adds/renames/removes something through the UI; if you're about to act on an entity/automation, verify it exists via `ha_search` / `ha_get_state` / `ha_get_entity` first.

   > **Maintenance:** After making changes to the HA setup (automations, helpers, integrations, areas, etc.), update this file to reflect the current state.

   *Not yet populated — run `/state` to generate an inventory from your live Home Assistant instance.*
   ```

   **CHANGELOG.md:**
   ```markdown
   # Changelog

   Log of changes made to this Home Assistant instance.
   ```

   **DOCS-CHANGELOG.md:**
   ```markdown
   # Docs Changelog

   Log of changes to this repo's own files/structure (CLAUDE.md, STATE.md, README.md, conventions). For changes that mutate the live Home Assistant instance, see `CHANGELOG.md` instead.
   ```

   **ISSUES.md:**
   ```markdown
   # Issues

   Observations flagged while working on something else — not acted on unless asked. Every entry starts as `TBD`; update the status once reviewed (e.g. `Fixed`, `Won't fix`, `Ignored`) rather than deleting the line, so there's a record.

   ## TBD
   ```

   **IDEAS.md:**
   ```markdown
   # Ideas

   Speculative, unscheduled ideas — bigger or more open-ended than an `ISSUES.md` entry. Not acted on unless asked.
   ```

   **VERIFY.md:**
   ```markdown
   # Verify

   Home Assistant changes that needed verification (per the `ha-verify` skill / `CLAUDE.md`'s Process section) but couldn't be confirmed live at the time — usually because the test is gated by a real-world condition (time of day, physical presence, a sensor event) that wasn't true when the change was made. Each entry records what to verify, the acceptance criteria, and why it was blocked, so it isn't silently forgotten.

   Running `/verify` with no arguments works through the `Pending` entries below. Once confirmed, move the entry to `Verified` (with the date and outcome) rather than deleting it, so there's a record — matching `ISSUES.md`'s convention of updating status in place.

   ## Pending

   ## Verified
   ```

3. **Append the first entry to the now-empty `DOCS-CHANGELOG.md`** recording this reset itself (today's date, ordered list per the usual changelog format):
   ```markdown
   ## {today's date}

   1. Ran `/new-setup` to reset `STATE.md`, `CHANGELOG.md`, `ISSUES.md`, `IDEAS.md`, and `VERIFY.md` to empty skeletons for a new Home Assistant instance.
   ```

4. **Commit** if this is a git repository (it should be, per the README's template instructions to `git init` before running this) — one commit, e.g. `git add -A && git commit -m "Reset instance-specific docs via /new-setup"`.

5. **Tell the user the next steps**: set up `.mcp.json` per `README.md`'s setup section (if not already done), then run `/state` to populate `STATE.md` from their live instance, and optionally `/conventions` to audit it against `CLAUDE.md`'s naming conventions.

# CLAUDE.md - Home Assistant Setup

This file provides context for Claude Code when working with this Home Assistant instance. It holds durable process and policy — not a snapshot of current entities/automations/helpers, which would go stale and mislead. For a point-in-time inventory (entity/device counts, areas, integration device counts, full automation list, helpers, sensors, dashboards, key devices), see `STATE.md` — read it only when you need orientation, and never trust it over a live MCP query (`ha_search`, `ha_get_state`, `ha_get_entity`, etc.) for anything you're about to act on. For opinionated naming/icon/entity-pattern conventions, see `CONVENTIONS.md`. For surprising technical quirks/integration gotchas, see `GOTCHAS.md`.

> **Maintenance:** After making changes to the HA setup (automations, helpers, integrations, areas, etc.), update `STATE.md` to reflect the current state. Update this file (`CLAUDE.md`) only when a convention, policy, or pattern changes.

## Process

- **Before any persistent config change** (automations, scripts, scenes, helpers, dashboards, entities, areas, etc.), consult the `home-assistant-best-practices` skill ("Get Home Assistant Best Practices Skill Guide") and read the reference file(s) it points to for the task at hand.
- **Before touching any entity or automation** — not just renames/disables — search for consumers/usages first (automations, scripts, scenes, dashboards). This is a single live HA instance with no staging environment, so there's no safety net if a change breaks something downstream.
- **No domain needs extra confirmation by default** (locks, alarm, cameras included) — treat all entities the same unless told otherwise for a specific case.
- **If you notice an inconsistency, stale entity, or improvement opportunity while working on something else**, don't act on it — add it to `ISSUES.md` under a `TBD` entry instead. Only fix it if asked.
- **When an `ISSUES.md` entry gets fixed**, delete its line entirely rather than marking it `Fixed:` — git history and `CHANGELOG.md`/`DOCS-CHANGELOG.md` already record what happened. Entries resolved as `Won't fix`/`Ignored` (no actual change made) keep their line with that status prefix instead, since there's no other record of the decision.
- **Before applying any fix** (an `ISSUES.md` entry or otherwise), describe the planned approach and wait for the user's explicit confirmation before acting on it — they may have input that changes the approach.
- **When you have clarifying questions, ask them one at a time** rather than bundling several into a single multi-question prompt — easier to give granular feedback on one question before moving to the next.
- **After any persistent config change**, run it through the `ha-verify` skill before logging it — propose acceptance criteria, set up baseline state, trigger it (with the user for real-world events), and assert the outcome via live MCP queries. Only proceed to the changelog step below once it passes. The same skill applies standalone when just debugging a reported problem, with no changelog step needed in that case.
- **After any change that mutates server state** (the live HA instance — automations, helpers, entities, etc.), once verified, append an entry to `CHANGELOG.md` in the repo root.
- **After any change to this repo's own files/structure** (CLAUDE.md, STATE.md, README.md, conventions — not the live HA instance), append an entry to `DOCS-CHANGELOG.md` instead.
- Both changelogs use the same format:

  ```
  ## {year}-{month}-{day}

  1. {description}
  ```

  Use an ordered (numbered) list, not bullets — entries within a day's section represent logical steps in sequence. If today's date already has a section, append a new numbered item under it (continuing the numbering) rather than creating a new section. Each changelog is chronological — new dates are added at the bottom.
- **Before committing any change to a `.md` file**, run `make lint-md` and fix anything it reports (config: `.markdownlint-cli2.jsonc`). Requires Node.js/npx.
- **After updating either changelog**, commit the change immediately (e.g. `git add CHANGELOG.md && git commit -m "..."`). Each mutation gets its own commit — keep history granular, don't batch multiple changes into one commit.
- **When pushing to remote**, use `make push` (not `git push` directly).
- **After adding a new Claude Code slash command** (`.claude/commands/*.md`), add it to the "Available Commands" list in `README.md`.
- **Avoid writing personal names into committed files** (this repo is public). Use generic placeholders instead — "primary user" / "partner", or "Person 1" / "Person 2" if a relationship label doesn't fit, or "[redacted]" if even that's unclear. This applies to prose/framing text; literal entity_ids and automation alias strings (e.g. `person.ben`, `Presence: Carol at Driveway`) are exempt since they're factual records of the live system, not promotional copy, and genericizing them would make docs stop matching reality.

## Goals
- Convenience & comfort
- Energy efficiency
- Security & safety

## Household Context

- **Cleaners** visit fortnightly while residents are usually home — no automation impact.
- **Pet sitters** visit while `input_boolean.holiday_mode` is on (away). This is why holiday mode has front-door-triggered light automations (to assist the sitter) and alerts for doors opening / lights turning on unexpectedly — see `Holiday:`/`Alert: Holiday *` automations in `STATE.md`.

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
- **See `GOTCHAS.md`** for why this script calls `media_player.select_source`/`media_player.media_play` on the Spotify entity rather than the Cast entity directly.

### Scripts
- `script.announce_speaker` — Reusable TTS announcement with chime, volume management, and playback resume. Fields: `media_players` (required), `message` (required), `volume` (default 0.8), `chime` (optional media-source URL), `state_helper` (optional input_text for state copy), `spotify_entity` (optional Spotify entity for reliable resume). Call via `script.turn_on` with `variables:` to pass fields reliably.

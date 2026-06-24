# hass

Claude Code context for a Home Assistant instance.

This repo doesn't contain Home Assistant config (no YAML, no `.storage/`). HA itself is managed live via the [hass MCP server](.mcp.json) — Claude reads/writes entities, automations, helpers, etc. directly through it. This repo just holds the context Claude needs to do that well.

## Contents

- `CLAUDE.md` — durable process and policy (workflow rules, household context, automation patterns). Auto-loaded into context; kept free of point-in-time state so it can't go stale.
- `CONVENTIONS.md` — opinionated naming/icon/entity-pattern conventions. Audited against the live instance via `/conventions`.
- `GOTCHAS.md` — surprising technical quirks/integration behavior worth knowing before you hit them.
- `Makefile` / `.markdownlint-cli2.jsonc` — `make lint-md` lints all root-level `.md` files (run before every commit that touches one); `.claude/**` is excluded since slash-command/skill files intentionally skip a top-level heading.
- `STATE.md` — point-in-time inventory snapshot (entity/device counts, areas, automations, helpers, sensors, dashboards). Read on demand, not auto-loaded; never trusted over a live MCP query.
- `CHANGELOG.md` — log of changes made to the HA instance via Claude.
- `DOCS-CHANGELOG.md` — log of changes to this repo's own files/structure (not the HA instance).
- `ISSUES.md` — observations flagged during other work but not acted on; each starts `TBD` until reviewed.
- `IDEAS.md` — speculative, unscheduled ideas bigger than an `ISSUES.md` entry; not acted on unless asked.
- `VERIFY.md` — pending `ha-verify` checks that couldn't be confirmed live yet (time-of-day/presence/physical-trigger gated), with acceptance criteria for when to retry.

## Available Commands

- `/state` — revalidates `STATE.md` against the live HA instance and corrects any drift (`.claude/commands/state.md`).
- `/conventions` — audits the live HA instance against `CLAUDE.md` conventions (naming, icons, Hue groups, entity patterns), reports findings, logs them to `ISSUES.md` (`.claude/commands/conventions.md`).
- `/issues` — lists open `ISSUES.md` entries and works through one interactively until resolved (`.claude/commands/issues.md`).
- `/rename` — proposes a better name for a device/entity, checks consumers, then renames and patches them once approved (`.claude/commands/rename.md`).
- `/verify` — runs the `ha-verify` testing loop against a change or a reported problem (`.claude/commands/verify.md`).
- `/new-setup` — resets the instance-specific docs (`STATE.md`, `CHANGELOG.md`, `DOCS-CHANGELOG.md`, `ISSUES.md`, `IDEAS.md`, `VERIFY.md`) to empty skeletons; for starting a fresh fork of this repo (`.claude/commands/new-setup.md`).

## Skills

- `ha-verify` — proposes acceptance criteria, sets up baseline state, has the user trigger real-world events, and asserts the outcome via live MCP queries. Auto-triggers after persistent config changes (before the changelog step) and standalone for debugging (`.claude/skills/ha-verify/SKILL.md`).

## Setup on a new machine

**Requirements:** Node.js/npx (for `make lint-md`, run via `npx markdownlint-cli2`).

1. Clone this repo.
2. Create `.mcp.json` (gitignored — contains a private connection token) pointing at the HA MCP server:

   ```json
   {
     "mcpServers": {
       "hass": {
         "type": "http",
         "url": "http://<ha-host>:<port>/<private-token>"
       }
     }
   }
   ```

3. Open the repo in Claude Code — `CLAUDE.md` is loaded automatically as project context.

## Using this as a template

`CLAUDE.md`, the `.claude/commands/`, and the `.claude/skills/` are generic — they describe conventions and workflows for *any* Home Assistant instance, not facts about a specific one. `STATE.md`, `CHANGELOG.md`, `DOCS-CHANGELOG.md`, `ISSUES.md`, `IDEAS.md`, and `VERIFY.md` are the opposite: logs/snapshots of one household's actual setup and history. To reuse this repo for your own instance:

1. Clone the repo, then drop its git history and start your own: `rm -rf .git && git init`.
2. Set up `.mcp.json` per the steps above, pointing at your own Home Assistant instance.
3. Open the repo in Claude Code and run `/new-setup` to reset the instance-specific docs to empty skeletons.
4. Run `/state` to populate `STATE.md` from your live instance, and optionally `/conventions` to audit it against `CLAUDE.md`'s naming conventions.

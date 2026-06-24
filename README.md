# hass

Claude Code context for Ben Rowe's Home Assistant instance.

This repo doesn't contain Home Assistant config (no YAML, no `.storage/`). HA itself is managed live via the [hass MCP server](.mcp.json) — Claude reads/writes entities, automations, helpers, etc. directly through it. This repo just holds the context Claude needs to do that well.

## Contents

- `CLAUDE.md` — durable conventions and policies (naming, icons, automation patterns, integration quirks). Auto-loaded into context; kept free of point-in-time state so it can't go stale.
- `STATE.md` — point-in-time inventory snapshot (entity/device counts, areas, automations, helpers, sensors, dashboards). Read on demand, not auto-loaded; never trusted over a live MCP query.
- `CHANGELOG.md` — log of changes made to the HA instance via Claude.
- `DOCS-CHANGELOG.md` — log of changes to this repo's own files/structure (not the HA instance).
- `ISSUES.md` — observations flagged during other work but not acted on; each starts `TBD` until reviewed.

## Available Commands

- `/state` — revalidates `STATE.md` against the live HA instance and corrects any drift (`.claude/commands/state.md`).
- `/conventions` — audits the live HA instance against `CLAUDE.md` conventions (naming, icons, Hue groups, entity patterns), reports findings, logs them to `ISSUES.md` (`.claude/commands/conventions.md`).
- `/issue` — lists open `ISSUES.md` entries and works through one interactively until resolved (`.claude/commands/issue.md`).

## Setup on a new machine

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

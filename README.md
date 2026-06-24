# hass

Claude Code context for Ben Rowe's Home Assistant instance.

This repo doesn't contain Home Assistant config (no YAML, no `.storage/`). HA itself is managed live via the [hass MCP server](.mcp.json) — Claude reads/writes entities, automations, helpers, etc. directly through it. This repo just holds the context Claude needs to do that well.

## Contents

- `CLAUDE.md` — current state of the HA instance: areas, integrations, automation categories/naming conventions, helpers, sensors, key entities. Kept up to date after changes are made via Claude.

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

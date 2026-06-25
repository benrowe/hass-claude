# GOTCHAS.md - Technical Quirks & Surprising Behavior

Hard technical constraints and surprising integration/HA behavior discovered while working on this instance — not naming opinions (see `CONVENTIONS.md`) and not process/policy (see `CLAUDE.md`). These are things that will bite you if you don't know about them; log a new entry whenever one is found, even if it isn't fixed yet.

> **Maintenance:** Add an entry whenever you hit a surprising integration/HA behavior, even if there's no fix — knowing about it is the point.

## Hue Groups

**Do not use Hue groups (Bridge-side room/zone groups).** They cause sync/state issues with HA. If a Hue group entity is found, disable it rather than using it for automations. See `STATE.md` for the current list of disabled groups.

## Google Cast TTS Tears Down Spotify Cast Sessions

Google Cast TTS (Default Media Receiver) tears down any active Spotify Cast session. Resuming Spotify requires calling `media_player.select_source` + `media_player.media_play` on the Spotify integration entity (e.g. `media_player.spotify_<account_id>`), not on the Cast entity directly. See `script.announce_speaker` in `CLAUDE.md`'s Key Automation Patterns for how this is handled.

## Renaming an Automation's Alias Doesn't Update Its Displayed Name

Changing an automation's `alias` field via `ha_config_set_automation` updates `original_name` in the entity registry, but if the entity already has a custom `name` override stored (set automatically the first time an automation is created/edited through the UI, even without an explicit rename), that override keeps showing the old text in the UI and in `friendly_name`/state attributes — the alias change is real but invisible. Confirmed via `ha_get_entity`: `original_name` reflects the new alias correctly while `name` still holds the stale string. This has hit two separate automation renames in this repo's history (the Garage override automations, and `Front Door: Motion Lights` → `Entry: Motion`) — in both cases, attempts to clear the override via `ha_set_entity(name='')` from the MCP tooling repeatedly failed with the call going out with no parameters attached (a tool-call malfunction, not a permissions issue), so the user cleared it manually via Settings → Automations → (automation) → rename/clear name field in the HA UI.

**When renaming an automation's alias going forward:** check `ha_get_entity` afterward — if `name` differs from the new `original_name`, the registry override needs clearing. If the MCP `ha_set_entity(name='')` call won't go through, ask the user to clear it manually in the UI rather than retrying blindly.

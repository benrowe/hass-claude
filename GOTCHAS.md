# GOTCHAS.md - Technical Quirks & Surprising Behavior

Hard technical constraints and surprising integration/HA behavior discovered while working on this instance — not naming opinions (see `CONVENTIONS.md`) and not process/policy (see `CLAUDE.md`). These are things that will bite you if you don't know about them; log a new entry whenever one is found, even if it isn't fixed yet.

> **Maintenance:** Add an entry whenever you hit a surprising integration/HA behavior, even if there's no fix — knowing about it is the point.

## Hue Groups

**Do not use Hue groups (Bridge-side room/zone groups).** They cause sync/state issues with HA. If a Hue group entity is found, disable it rather than using it for automations. See `STATE.md` for the current list of disabled groups.

## Google Cast TTS Tears Down Spotify Cast Sessions

Google Cast TTS (Default Media Receiver) tears down any active Spotify Cast session. Resuming Spotify requires calling `media_player.select_source` + `media_player.media_play` on the Spotify integration entity (e.g. `media_player.spotify_<account_id>`), not on the Cast entity directly. See `script.announce_speaker` in `CLAUDE.md`'s Key Automation Patterns for how this is handled.

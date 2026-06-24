---
description: List open issues from ISSUES.md and work through one interactively
---

Help work through the backlog in `ISSUES.md`. The goal is to reduce it to a minimum over time, not just to read it.

## Steps

1. Read `ISSUES.md` and list every `TBD` entry as a numbered list (and any other unresolved status, if the format has grown beyond `TBD`/resolved).
2. Ask the user which one to work on (single question, one at a time per `CLAUDE.md`'s Process section — don't bundle).
3. Once picked, discuss it with the user and work it through to a resolution. If it turns into an actual change to the live HA instance or to repo docs, follow the normal `CLAUDE.md` Process rules (consult the best-practices skill, search consumers before touching anything, log to `CHANGELOG.md`/`DOCS-CHANGELOG.md` as appropriate, commit).
4. Once resolved (fixed, won't-fix, or otherwise settled), update that issue's line in `ISSUES.md` to reflect the outcome (e.g. prefix with `Fixed:`, `Won't fix:`, `Ignored:`) rather than deleting it, so there's a record. Commit this alongside the resolving change.
5. Ask if the user wants to keep going with another issue or stop here.

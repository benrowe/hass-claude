---
description: Capture a new idea into IDEAS.md via a short discussion to flesh it out first
---

Help the user think through a new idea and record it in `IDEAS.md`. The goal is a well-formed entry, not a quick dump — so have a brief discussion before writing anything.

The argument (if provided) is the user's initial overview of the idea. If no argument is given, ask them to describe it in a sentence or two first.

## Steps

1. **Acknowledge the idea** and ask one focused question to understand what's missing or unclear — e.g. what problem it solves, what would trigger acting on it, or what the rough implementation path looks like. Don't ask multiple questions at once (per `CLAUDE.md`'s Process section).

2. **Continue the discussion** with follow-up questions as needed — one at a time — until you have a clear enough picture to write a good entry. Aim for:
   - A descriptive heading (what the idea is, not vague)
   - The motivation (why it's worth doing, what it unlocks)
   - What it would take (rough requirements, key steps, or blockers)
   - Any known gotchas, dependencies, or conditions under which it becomes more/less worthwhile
   - Any specific things worth researching before acting on it

3. **Draft the entry** in the same format as existing `IDEAS.md` ideas:
   - `## Heading` — concise noun phrase
   - Prose paragraph(s) explaining the idea and motivation
   - Bullet list of what's needed / key steps, if there are discrete pieces
   - `research: <topic>` line(s) at the end if there's something to look up first (omit if nothing to research)

   Present the draft to the user for review before writing it.

4. **Once approved**, read `IDEAS.md`, append the new entry at the bottom, run `make lint-md`, and commit with `git add IDEAS.md && git commit`.

5. **Update `DOCS-CHANGELOG.md`** with a one-line entry noting the idea was added, then commit that too.

6. **Update `README.md`** only if the command itself is new and not yet listed under "Available Commands" — otherwise skip this step.

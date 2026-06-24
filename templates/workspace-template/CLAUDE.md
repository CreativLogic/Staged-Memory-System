# SMS Workspace — Agent Identity

<!-- REFERENCE: REFERENCE-MANUAL.md -->

This is a Staged Memory System workspace. Follow the five-layer loading protocol. Load only what you need for the current task.

## Five Layers

| Layer | File | When |
|-------|------|------|
| 0 | `CLAUDE.md` (this file) | Every session start |
| 1 | `CONTEXT.md` | After Layer 0 — route the task |
| 2 | `stages/<Name>/CONTEXT.md` | When executing a specific stage |
| 3 | `_config/` `_shared/` `resources/` | When producing output that needs rules |
| 4 | `databases/` `stages/<Name>/output/` | When working with live data |

## Hard Rules

- Filesystem first — read files before computing or asking
- Never ask for context that exists in a file
- Load a layer, check if you have enough, stop if you do
- Stage folders use capital letters, no numbers
- Never delete without backup to `archives/`

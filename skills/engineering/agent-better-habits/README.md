# agent-better-habits

A working discipline that counters the bad habits coding agents tend to fall into.

## What it fixes

| Habit | Rule |
|---|---|
| Fixing bugs that were never confirmed | Reproduce before fixing |
| Saying "fixed" without proof | Show evidence from this session, or say it isn't verified |
| Calling library APIs that don't exist | Confirm the API exists in the installed version first |
| Shotgun fixes that leave failed attempts behind | Diagnose before retrying and remove failed attempts |
| Silencing errors (type escapes, empty catches, skipped tests) | Fix the root cause or report the blocker |
| Hand-rolling what a library already does | Check existing helpers, the standard library, and dependencies first |
| Bloated diffs and edits to unrelated code | Size the change to the request and check callers before changing helpers |
| Adding comments instead of changing code | Change the behavior and keep comments true |
| Leaving stale and deprecated code in place | Delete what you replace |
| Long apologies and dragging a task out over many turns | Acknowledge in one line and fix right away |
| Rules that only fit one incident | Write rules about the general behavior |

## How to use it

The agent loads it on its own for any task that writes, edits, debugs, or reviews code, and whenever you ask it to "remember" something or update its rules. You don't need to invoke it.

To apply it explicitly, mention it:

```
Use agent-better-habits. Fix the date bug in the invoice export.
```

Before the agent reports a task as done, it works through the checklist at the end of `SKILL.md`.

## Files

- `SKILL.md`: the rules and the final checklist.

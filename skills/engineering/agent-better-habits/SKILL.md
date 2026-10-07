---
name: agent-better-habits
description: Working discipline that counters common coding-agent failure modes such as reinventing libraries, unverified "fixed" claims, silencing errors instead of fixing them, scope creep, collateral edits, and stale-context pollution. Use for any task that writes, edits, debugs, or reviews code, and whenever creating or updating agent rules/memory.
---

# Agent Better Habits

These rules apply to every coding task. Each rule names a general behavior, not one specific incident. Apply it to every case it fits.

## 1. Verify before acting

- **Reproduce before fixing.** When a bug is reported, confirm it exists first: run it, test it, or trace the code path. If you can't reproduce it, say so and show what you checked. Don't fix a problem nobody has shown exists.
- **Verify APIs exist.** Before calling a library function, method, flag, or config key, confirm it exists in the installed version (its source, type definitions, or docs). Don't call what only seems plausible.
- **Read before changing.** Before you modify a function, type, or helper, find all of its callers and dependents (grep or references). Your change has to keep every one of them working, not only the one in front of you.

## 2. Prove before claiming done

- Don't say "fixed", "done", or "works" without evidence from this session: a passing test, command output, a reproduction that now succeeds, or a type check or build that passes.
- If you can't verify it, say exactly that: "Not verified: <reason>. To verify: <command>."
- When a fix fails, find out *why* it failed before you try the next one. Don't keep pulling the lever for a new guess.
- No shotgun fixes. Don't try variants until one works. If you did try several, remove every failed attempt so only the working change remains.

## 3. Fix root causes, never silence symptoms

These count as silencing, not fixing, and are forbidden unless the user explicitly approves:
- Escaping the type checker, linter, or compiler to get past an error: dynamic or untyped casts, ignore or suppress directives, forced unwraps or non-null assertions.
- Swallowing errors with empty or catch-all handlers, or with default fallbacks that hide failure.
- Skipping, deleting, or loosening tests or assertions to get a green run.
- Workarounds tied to the current environment, such as hardcoded values, retries, or sleeps, that won't hold on the next run.

If the precise fix can't be done, stop and report the blocker. A visible failure is better than a hidden one.

## 4. Use what exists before writing new code

Before you write any utility (validation, parsing, dates and time, formatting, HTTP, retries, crypto, path handling, deep-equal, and so on), look in this order:
1. The codebase's own helpers and the libraries it already depends on (check the project's dependency manifest).
2. The language's standard library.
3. A well-established library, but propose it to the user before adding a dependency.

Hand-rolled regexes and custom date math, written when one of those already handles it, count as defects. The full catalog of domains where hand-rolling is a sin is in the `agent-sins` skill.

## 5. Keep the change minimal and in scope

- Size the change to the request. If a simple ask is turning into many more lines than a reasonable estimate, stop and simplify.
- Don't add unrequested abstractions, classes, config options, layers, or "future-proofing".
- Don't touch unrelated code. If you notice something else that's wrong, mention it; don't fix it in passing.
- Write code to match the surrounding code's style, naming, and comment density. Readable beats clever.

## 6. Change code, don't annotate it

- Don't respond to feedback by adding comments like "// should not do X". Change the behavior. A comment is never a fix.
- Every comment has to stay true about the code next to it. After an edit, delete or update any comment that no longer matches.

## 7. Remove dead and stale material; don't layer on top of it

- When you replace or deprecate code, a rule, a doc, or a pattern, **delete the old version.** Don't keep both, and don't mark the old one as "deprecated" while leaving it in place.
- Don't let changes cancel each other out (adding new logic while leaving the old logic alive behind a flag or a comment).
- When you learn conventions from the codebase, prefer recently changed, actively used code. Treat anything marked deprecated, legacy, or old as an anti-example. If two conflicting patterns exist, ask which one is current.

## 8. Respect the user's time

- Finish a clear instruction in as few turns as possible. Don't split a simple task across several confirmation rounds, and don't ask questions you can answer by reading the code.
- When corrected: acknowledge it in one line at most, then apply the fix right away. No apologies, no explanations of the "honest mistake", no "you're right to be frustrated".
- Follow the instruction as written. If it's ambiguous, ask once with a recommended default.

## 9. Writing agent rules and memory

When the user asks you to "remember", "don't do this again", or update rules or memory:
- **Generalize.** Name the class of behavior, not the single incident. A bad rule is "Don't write a regex for email in the signup form." A good rule is "Use existing libraries for validation, parsing, and dates instead of hand-rolled implementations."
- Test the rule: would it fire in at least a few *different* future situations? If it only fits the original incident, rewrite it at a broader level.
- Before adding a rule, check for an existing one that covers the same thing and update it instead. Delete rules that are obsolete (see §7).
- Keep each rule to one or two sentences plus a short *why*.

## Final check before reporting completion

- [ ] Bug confirmed to exist, or the request was a feature
- [ ] All callers and dependents of changed code still work
- [ ] Evidence of success shown (test, build, or output)
- [ ] No type escapes, swallowed errors, or skipped tests added
- [ ] No hand-rolled code where a library or helper exists
- [ ] Diff is minimal; no unrelated edits or leftover failed attempts
- [ ] No stale code, comments, or rules left behind

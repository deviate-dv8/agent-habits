# Codebase token-efficiency audit

Score the current codebase against the patterns in `../SKILL.md`. Run real queries and cite actual files. Don't guess.

## Steps

1. **Tool accessibility**: run test queries with `grep`, `find`, and `ls`.
   - Can one grep find a function by name?
   - Can one find locate all of a feature's files?
   - Does the directory structure show intent without deep traversal?
2. **File structure**: sample 5–10 files from across the codebase.
   - Are layers or concerns in separate files?
   - Is the content dense and focused, or sparse and scattered?
   - Are related files grouped together?
3. **Documentation**
   - Is there a single large doc that gets loaded on every lookup?
   - Are docs split by concern, so an agent can grab only what it needs?
4. **Architecture context**
   - Is there a clear architecture overview, linked from `AGENTS.md`/`CLAUDE.md`?
   - Do names reveal module boundaries?
5. **Localization**
   - Is external data (issues, specs) available as local markdown, or does every lookup go through an MCP or API?

## Output

| Category | Score | Findings | Recommendations |
|---|---|---|---|
| Tool Accessibility | /5 | | |
| File Structure | /5 | | |
| Documentation | /5 | | |
| Architecture Context | /5 | | |
| Localization | /5 | | |
| **Total** | **/25** | | |

Then add:
- **Top 3 friction points**: where agents waste the most tokens.
- **Quick wins**: changes that help right away.
- **Long-term improvements**: structural changes.

Every finding must cite a file path or a command result.

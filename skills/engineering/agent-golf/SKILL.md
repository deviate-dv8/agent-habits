---
name: agent-golf
description: Patterns that let agents reach correct results in fewer tool calls and tokens, covering how to search, what context to load, and how to structure code, docs, and dev tooling so agents can query them cheaply. Use when navigating or exploring a codebase, when designing file/folder/doc layout, when writing agent docs (AGENTS.md, CLAUDE.md), when building scripts or CLIs that agents will run, when improving an agent framework, or when asked to audit or score how agent-friendly or token-efficient a codebase is.
---

# Agent Golf

The goal is fewer actions for better, faster results. Most of an agent's cost goes into **ingesting data**, not writing code, so cut ingestion first. These patterns apply to any language or stack.

## Model allocation

- Use cheaper, execution-oriented models for coding and file generation. Use reasoning models for planning, analysis, and heavy MCP work.
- Coding and file generation are usually a small share of the spend, and reasoning a larger share. Ingestion outweighs both, so optimize it first.

## Navigating

- **Use the cheapest tool that answers the question.** Go in this order: `grep`/`rg`, then `find`/`fd`/`ls`, then MCP or API calls, then multi-step command pipelines you write yourself. Each step down costs more tokens to run and to read.
- **Prefer local text over remote structured data.** Check for local markdown snapshots (issues, specs, tickets) before calling an MCP or API. JSON responses carry brackets and metadata you have to parse every time.
- **Read the architecture doc first.** If `ARCHITECTURE.md`, `AGENTS.md`, or a similar overview exists, read it before grepping the whole repo to work out the structure.
- **Load narrow, dense context.** A small amount of information-rich context is better than a large amount of sparse context. Read the function or section you need, not the whole file. Use line-range reads (`sed -n`, `head`, offset/limit) instead of full-file reads.

## Structuring code and docs for agents

- **One concern per file.** Keep layers in separate files (for example request handling, business logic, and data access), so pulling one doesn't pull them all.
- **Group related files in one folder**, so finding a feature takes one scan.
- **Use names that are modular and grep-able.** A feature's files and symbols should share a name you can find with one search (for example `invoice_handler`, `invoice_service`, `invoice_repo`).
- **Split docs by concern** (`setup.md`, `architecture.md`, `api.md`). Don't write one large doc that gets loaded in full on every lookup.
- **Write down the architecture up front** in a short doc linked from `AGENTS.md`/`CLAUDE.md`.

## Designing commands agents run

**Single command, zero decisions.** Every flag or choice an agent has to make on a routine task costs tokens and turns, because it has to work out the answer again each time. Collapse routine choices into one idempotent command that is always correct.

- **Derive state; don't register it.** Let the filesystem or the tool be the source of truth, so there's nothing to pass and nothing to remember.
- **Use the tool's own change detection** instead of writing your own hashing or forcing a full rebuild.
- **Isolate the state that causes side effects.** A shared config that every instance loads means one agent's edit affects all of them. Split shared and per-instance config.
- **Make safe behavior the default.** A warning in a doc isn't a fix. The script itself should refuse destructive actions (for example, discarding uncommitted work) unless an explicit `--force` is given.
- **Close known risks before calling it stable,** for example a race between concurrent agents (use a transient lock). A risk you flagged but didn't fix is still a risk.
- **Verify isolation and idempotency by running it** twice, or concurrently, and compare the results. Reasoning about the config isn't verification.

## Auditing a codebase

When asked to audit or score a codebase, follow [references/audit.md](references/audit.md).

## Checklist

| Pattern | Benefit |
|---|---|
| grep before MCP/API | Lower token cost |
| Local markdown before MCP JSON | No parsing overhead |
| Architecture doc up front | No repo-wide greps |
| One concern per file | Ingest only what's needed |
| Related files grouped together | Fewer scanning cycles |
| Docs split by concern | Only relevant context gets loaded |
| Modular, grep-able names | One-shot retrieval |
| One idempotent command per routine task | No decision overhead |
| Safe defaults in the tool, not in docs | Holds even when nobody reads the doc |

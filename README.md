# Agent Habits

A collection of [Agent Skills](https://agentskills.io) that fix the bad habits coding agents fall into and help them get better results in fewer turns, tool calls, and tokens. The skills come from patterns seen across many projects and session records.

Each skill is a small directory that can be combined with others and edited freely.

## Install

```bash
# All skills
npx skills@latest add deviate-dv8/agent-habits

# Or copy a single skill by hand
cp -r skills/engineering/agent-better-habits ~/.claude/skills/
```

## Skills

### Engineering

| Skill | Invoked by | What it does |
|---|---|---|
| [agent-better-habits](skills/engineering/agent-better-habits/README.md) | Model | Working discipline for any coding task: verify before fixing, prove before claiming done, never silence errors, reuse libraries, keep diffs minimal, remove stale material, write rules in general terms. |
| [agent-golf](skills/engineering/agent-golf/README.md) | Model | Analyzes how easily agents can navigate a repo. Badly structured repos are expensive to run agents on, and well-structured ones are cheap. |

**Invoked by:**
- **Model**: a reusable discipline the agent loads on its own when the `description` matches the task.
- **User**: a workflow you start explicitly with `/skill-name`.

## Layout

```
skills/
  <category>/
    <skill-name>/
      SKILL.md        # required: frontmatter + instructions
      README.md       # for humans: what it does and how to use it
      references/     # optional: detail loaded only when needed
      scripts/        # optional: executable helpers
      assets/         # optional: templates, data
```

See [CLAUDE.md](CLAUDE.md) for authoring rules.

## License

MIT

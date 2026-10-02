# Authoring skills in this repo

Every skill follows the [Agent Skills spec](https://agentskills.io/specification).

## Structure

- Path: `skills/<category>/<skill-name>/SKILL.md`. Current category: `engineering`. Add a category only when a skill doesn't fit any existing one.
- The `name` frontmatter field must match the directory name. Use lowercase letters, digits, and single hyphens, up to 64 characters.
- The `description` field is required and limited to 1024 characters. Say what the skill does **and** when to use it, using the keywords an agent would match against.
- Keep `SKILL.md` under 500 lines (about 5k tokens). Put detail in `references/`, linked one level deep from `SKILL.md`.

## Writing rules

- Keep skills free of project-specific details: no personal names, internal hostnames, or private repo paths. Generalize them or leave them out. That applies to `references/` files too.
- Keep skills language-agnostic. Describe behaviors, not syntax, and when you need an example, use neutral names or show several ecosystems.
- Write each rule about a general type of behavior, not a single incident. A rule has to apply in several different future situations.
- Keep each rule to one or two sentences plus a short *why*. Use imperative voice.
- One skill covers one concern. Prefer several small skills that work together over one large skill.
- When you replace a rule or skill, delete the old one. Don't leave deprecated copies behind.

## Adding a skill

1. Create `skills/<category>/<skill-name>/SKILL.md`.
   Add a `README.md` next to it for humans that says what the skill does and how to use it.
2. Add a row to the Skills table in `README.md`, and say whether it's invoked by the model or the user.
3. If `skills-ref` is installed, validate with `skills-ref validate skills/<category>/<skill-name>`.

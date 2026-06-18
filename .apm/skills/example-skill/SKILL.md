---
name: example-skill
description: >-
  Use when the user asks for an example of how skills in this repo are
  structured, or as a template when authoring a new skill. Replace or delete
  this skill once you have real ones.
---

# Example Skill

This is a starter skill that demonstrates the apm skill format. The folder name
(`example-skill`) must match the `name` in the frontmatter above.

## How a skill is structured

- `SKILL.md` (this file) is required. Keep it under ~500 lines / 5000 tokens.
- The frontmatter `description` is the **trigger** — write it as "Use when …"
  so the agent knows when to activate this skill. Lead with user intent.
- Optional sibling folders are deployed alongside the skill:
  - `scripts/` — executable helpers the skill can run
  - `references/` — longer docs loaded on demand
  - `assets/` — templates, images
  - `examples/` — sample inputs/outputs

## Loading larger content

Keep this file focused. For anything long, put it in `references/` and point to
it so the agent loads it only when needed:

> For the full checklist, LOAD references/checklist.md.

## Next step

Delete this skill (or copy it) and author your own under
`.apm/skills/<your-skill-name>/SKILL.md`.

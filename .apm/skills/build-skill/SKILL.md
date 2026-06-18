---
name: build-skill
description: >-
  How to author a new agent skill in this apm repo. Use when the user asks how
  skills are structured, wants to scaffold or write a new skill, or needs the
  SKILL.md frontmatter rules. Covers folder layout, frontmatter constraints,
  writing the description trigger, progressive disclosure, and an authoring
  checklist.
---

# Build a Skill

A skill is a folder with a `SKILL.md` that teaches an agent a repeatable
capability. Only the `name` and `description` are pre-loaded into context; the
body and any sibling files load on demand, so long material costs almost nothing
until it is actually used.

Create a skill when you keep re-pasting the same instructions, checklist, or
multi-step procedure, or when a chunk of docs has become a *procedure* rather
than a *fact*.

## Authoring workflow

1. Create `.apm/skills/<skill-name>/SKILL.md`. The folder name MUST equal the
   frontmatter `name` (apm requirement).
2. Write the frontmatter (`name`, `description`); see constraints below.
3. Write the body: state *what to do*, not why. Assume the agent is already
   smart; only add context it doesn't already have.
4. Push anything long into `references/` and link to it (progressive disclosure).
5. Test by giving a fresh agent a real task and watching where it struggles.

## Frontmatter

Two fields are required:

```yaml
---
name: <kebab-case; must match the folder name>
description: <what it does AND when to use it>
---
```

- **`name`**: lowercase letters, numbers, hyphens only; 64 chars or fewer. Cannot
  contain the reserved words `claude` or `anthropic`. Gerund form reads well
  (`writing-tests`), but action/noun forms (`build-skill`, `pdf-processing`) are
  fine.
- **`description`**: non-empty, 1024 chars or fewer, written in **third person**.
  This is the trigger the agent matches on, so lead with intent and include
  concrete terms.
  - Good: `Extract text and tables from PDFs. Use when the user mentions PDFs, forms, or document extraction.`
  - Avoid: `Helps with documents` / `I can help you process files`.

Claude Code also supports optional fields (`allowed-tools`,
`disable-model-invocation`, `user-invocable`, `context: fork`, `argument-hint`,
and more). For the full list and when to use each, LOAD
[references/best-practices.md](references/best-practices.md).

## How a skill is structured

- `SKILL.md` (required) is the entrypoint. Keep it under ~500 lines / 5000
  tokens. It is an overview that points to detail, like a table of contents.
- Optional sibling folders are deployed alongside the skill and loaded only when
  referenced:
  - `scripts/`: executable helpers the agent runs (more reliable and cheaper
    than generated code; say whether to *run* or *read* each one)
  - `references/`: longer docs loaded on demand (API specs, deep guides)
  - `assets/`: templates, images, boilerplate
  - `examples/`: sample inputs/outputs that show the expected format

## Progressive disclosure

Keep `SKILL.md` focused; move the rest into sibling files and link them:

> For the full checklist, LOAD references/best-practices.md.

- Keep references **one level deep** from `SKILL.md`; the agent may only
  partially read a file reached through another reference.
- Use forward slashes in every path (`scripts/run.sh`), never backslashes.
- Add a table of contents to any reference file longer than ~100 lines.

## Checklist

- [ ] Folder name == frontmatter `name`
- [ ] `description` says what it does AND when to use it, in third person
- [ ] Body is concise and under ~500 lines
- [ ] Long material lives in `references/`, linked one level deep
- [ ] Scripts (if any) state run-vs-read and handle their own errors
- [ ] No time-sensitive wording; terminology is consistent throughout
- [ ] Tested with a real task on a fresh agent

For deeper guidance (degrees of freedom, feedback loops, evaluation-driven
development, and script anti-patterns), LOAD
[references/best-practices.md](references/best-practices.md).

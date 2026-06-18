# Skill authoring best practices

Distilled from Claude's official guidance:

- Best practices: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- Claude Code skills: https://code.claude.com/docs/en/skills

Skills follow the open [Agent Skills](https://agentskills.io) standard, so the
core (`SKILL.md` + frontmatter + sibling files) is portable across Claude,
Copilot, Cursor, and more. The optional frontmatter fields below are Claude
Code extensions.

## Contents

- Core principles
- Degrees of freedom
- Optional frontmatter (Claude Code)
- Progressive disclosure
- Workflows and feedback loops
- Scripts
- Anti-patterns
- Evaluation-driven development
- Pre-share checklist

## Core principles

- **Concise is key.** The context window is shared. Challenge every line: "Does
  the agent really need this? Can I assume it already knows this?" Cut
  explanations of common concepts.
- **Assume a capable agent.** Add only what is specific to *your* task, repo, or
  conventions, not general knowledge.
- **Consistent terminology.** Pick one term per concept and reuse it (always
  "field", never a mix of "field" / "box" / "element").
- **Standing instructions, not one-time steps.** Once invoked, `SKILL.md` stays
  in context for the session; write guidance meant to apply throughout the task.

## Degrees of freedom

Match specificity to how fragile the task is.

- **High freedom** (prose steps): many valid approaches; let the agent choose,
  e.g. a code-review process.
- **Medium freedom** (pseudocode / parameterized scripts): a preferred pattern
  exists but variation is OK, e.g. a report generator with options.
- **Low freedom** (one exact command, no flags): fragile, consistency-critical,
  must run in sequence, e.g. a DB migration: "Run exactly this; do not modify."

## Optional frontmatter (Claude Code)

All optional; only `description` is recommended.

| Field | Use |
| :-- | :-- |
| `allowed-tools` | Pre-approve tools (no per-use prompt) while the skill is active, e.g. `Bash(git add *) Bash(git commit *)`. |
| `disable-model-invocation: true` | Only the user can invoke it (`/name`). Use for side-effecting workflows like deploy/commit. |
| `user-invocable: false` | Only the agent can invoke it. Use for background knowledge that isn't a user action. |
| `argument-hint` | Autocomplete hint, e.g. `[issue-number]`. |
| `arguments` / `$ARGUMENTS` / `$0` | Pass and reference positional args in the body. |
| `context: fork` + `agent` | Run the skill as an isolated subagent (e.g. `agent: Explore`). Only meaningful when the body is a *task*, not reference knowledge. |
| `model` / `effort` | Override model or effort level while active. |
| `paths` | Glob patterns; auto-activate only when working on matching files. |

Dynamic context: a `` !`<command>` `` line (or a ` ```! ` block) runs before the
agent sees the content and is replaced by the command's output, e.g.
`` !`git diff HEAD` `` inlines the live diff.

## Progressive disclosure

- `SKILL.md` is a table of contents that points to detail loaded on demand.
- Keep references **one level deep**: files reached only through another
  reference may be read partially (e.g. via `head`), losing information.
- Organize by domain so unrelated context isn't loaded (`reference/finance.md`,
  `reference/sales.md`).
- Name files for their contents (`form_validation_rules.md`, not `doc2.md`).
- Forward slashes only; reference files over ~100 lines get a table of contents.

## Workflows and feedback loops

- Break complex tasks into clear, numbered steps. For multi-step work, give a
  checklist the agent copies into its response and ticks off.
- Add validate -> fix -> repeat loops where quality matters: "Run the validator;
  if it fails, fix and re-run; only proceed when it passes."
- For batch/destructive changes use plan -> validate -> execute: write a
  structured plan file, validate it with a script, then apply it.

## Scripts

- **Run vs. read.** Say which: "Run `analyze.py` to extract fields" (execute) vs.
  "See `analyze.py` for the algorithm" (read as reference). Prefer execution:
  more reliable, fewer tokens.
- **Solve, don't punt.** Handle errors in the script instead of leaving the agent
  to recover (catch `FileNotFoundError`, provide a default, etc.).
- **No voodoo constants.** Justify values in a comment (`MAX_RETRIES = 3  # most
  intermittent failures clear by the second retry`).
- **State dependencies.** List required packages; don't assume they're installed.
  Note that some environments have no network/package install at runtime.
- For MCP tools, use fully-qualified names: `ServerName:tool_name`.

## Anti-patterns

- Time-sensitive wording ("after August 2025, use the new API"). Put deprecated
  guidance in a collapsed "Old patterns" section instead.
- Offering many options. Give one default with an escape hatch, not a menu.
- Windows-style backslash paths.
- Vague descriptions or first/second-person voice in the `description`.
- Deeply nested references.

## Evaluation-driven development

Build evals *before* writing extensive docs, so the skill solves real problems:

1. Run the agent on representative tasks **without** the skill; record failures.
2. Write ~3 eval scenarios targeting those gaps.
3. Establish the no-skill baseline.
4. Write the minimum instructions needed to pass.
5. Iterate: develop with one agent ("A"), test with a fresh agent ("B") on real
   tasks, bring observations back to A. Strengthen `name`/`description` first
   when B fails to trigger the skill.

## Pre-share checklist

- [ ] `description` is specific, third person, and states what + when
- [ ] `SKILL.md` body under ~500 lines; long material split into files
- [ ] References one level deep; no time-sensitive info; consistent terms
- [ ] Examples are concrete, not abstract
- [ ] Scripts: explicit error handling, justified constants, listed deps, run-vs-read clear, forward slashes
- [ ] Validation/feedback loops for quality-critical steps
- [ ] At least three evals; tested with real usage (and across models you target)

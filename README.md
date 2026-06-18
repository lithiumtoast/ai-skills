# ai-skills

Reusable AI skills for Copilot, Claude, Cursor, OpenCode, Codex, Gemini, Windsurf, etc.

An [apm](https://github.com/microsoft/apm) (Agent Package Manager) package of
agent **skills**, shared across all my projects.

## Layout

```
ai-skills/
├── apm.yml                       # package manifest (includes: auto)
└── .apm/
    └── skills/
        └── <skill-name>/
            ├── SKILL.md          # required; frontmatter name MUST match folder
            ├── references/       # optional: long docs, loaded on demand
            ├── scripts/          # optional: executable helpers
            ├── assets/           # optional: templates/images
            └── examples/         # optional: samples
```

## Authoring a new skill

1. Create `.apm/skills/<skill-name>/SKILL.md`.
2. Frontmatter needs `name` (must equal the folder name) and `description`.
   Write the description as a trigger — start with "Use when …".
3. Keep `SKILL.md` under ~500 lines / 5000 tokens; push long content into
   `references/` and reference it with `LOAD references/<file>.md`.
4. Commit and push.

## Using these skills in a project

Install from GitHub and deploy to Claude Code:

```bash
apm install lithiumtoast/ai-skills -t claude        # pin a release: lithiumtoast/ai-skills#v1.0.0
```

This adds the dependency to that project's `apm.yml`, writes `apm.lock.yaml`,
and deploys the skills into `.claude/skills/`. Re-run `apm install` after you
push changes here. Commit `apm.yml` + `apm.lock.yaml` in the consumer project;
`apm_modules/` is auto-gitignored.

### Local-path alternative (this machine / offline)

```bash
apm install /Users/lstranks/d/ai-skills -t claude
```

## Releasing

Bump `version` in `apm.yml` and tag the commit when you want consumers to pin a
specific release:

```bash
git tag v1.1.0 && git push --tags
```

## License

[MIT](LICENSE) © Lucas Girouard-Stranks

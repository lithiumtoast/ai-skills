# ai-skills

[@lithiumtoast](https://github.com/lithiumtoast)'s reusable AI agent skills, packaged with [apm](https://github.com/microsoft/apm) (Agent Package Manager), portable across Claude, Copilot, Cursor, and more.

## Layout

```
ai-skills/
├── apm.yml                       # package manifest (type: hybrid, includes: auto)
└── .apm/
    ├── instructions/             # always-on house rules, compiled into AGENTS.md
    │   └── <name>.instructions.md
    └── skills/
        └── <skill-name>/
            ├── SKILL.md          # required; frontmatter name MUST match folder
            ├── references/       # optional: long docs, loaded on demand
            ├── scripts/          # optional: executable helpers
            ├── assets/           # optional: templates/images
            └── examples/         # optional: samples
```

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

# Commit message style

Reference for [../SKILL.md](../SKILL.md). Conventional Commits subject, Linus-style
body. Load this when drafting a commit message.

## Contents

- Conventional Commits types
- Common type confusions
- Subject rules
- Body rules (Linus-style)
- Footer
- Worked examples
- External references

## Conventional Commits types

Pick exactly one type per commit. If two genuinely fit, the slice is too broad:
split it.

| Type | When to use |
|---|---|
| `feat` | New user-facing behavior or capability. |
| `fix` | Correct broken behavior. |
| `refactor` | Restructure code without changing observable behavior. |
| `perf` | Performance improvement with the same observable behavior. |
| `docs` | Documentation only: READMEs, comments, design docs. |
| `test` | Add or fix tests; no production-code change. |
| `build` | Build system or dependency manifests: package manifests, lockfiles, Dockerfile. |
| `ci` | CI/CD configuration: pipelines, workflows, pre-commit hooks. |
| `chore` | Routine maintenance with no functional change: repo hygiene, `.gitignore`, tool config. |
| `style` | Formatting only: whitespace, import order, naming. No logic change. |
| `revert` | Revert a prior commit; reference the reverted SHA in the body. |

## Common type confusions

- *`feat` vs `fix`*: `feat` adds new behavior; `fix` corrects existing behavior.
- *`feat` vs `refactor`*: `feat` changes observable behavior; `refactor`
  restructures without changing it. A change that alters how something behaves
  is not a refactor, even when it edits a single file.
- *`refactor` vs `style`*: `refactor` changes structure; `style` only changes
  whitespace or formatting with no structural change.
- *`chore` vs `build`*: `build` is specifically build and dependency files;
  `chore` is broader maintenance.
- *`docs` vs `feat`*: a README, comment, or design doc is `docs`; new runnable
  behavior is `feat`, even when it ships as a config or script.

## Subject rules

- Imperative mood: "add", "fix", "remove", not "added", "fixes", "removed".
- 72 characters or fewer; target 50 or fewer.
- No trailing period.
- Do not repeat the type inside the subject text (`feat: feat add X` is wrong).
- Scope is an optional short component name in parentheses. Drop it when the
  change spans the whole project.

## Body rules (Linus-style)

- Wrap every line at 72 characters.
- Explain motivation and trade-offs. The diff already shows *what* changed; the
  body must explain *why*.
- Imperative voice. No "I", "we", "this PR", or "this commit". No marketing
  prose.
- Prefer prose. Use bullets only for genuinely distinct facts.
- Omit the body entirely when the subject is self-explanatory, such as typo or
  formatting-only fixes.

## Footer

Optional, one per line:

- `Refs: #123` references an issue.
- `Closes: #456` closes an issue.
- `BREAKING CHANGE: <description>` marks a breaking change.

No AI co-authorship trailers. The user is the author; add one only on explicit
request.

## Worked examples

Input describes the change; output is the full message.

*Example 1 (fix, with body):*

Input: guarded a null user that crashed the request handler.

```
fix(api): guard against null user in request handler

HandleAsync dereferenced request.User without checking it, crashing on
unauthenticated calls. Return 401 when the user is absent instead.
```

*Example 2 (refactor, with body):*

Input: extracted duplicated date parsing into one helper.

```
refactor: extract shared date parsing into parseTimestamp

Three call sites carried copies of the same parsing block, so a bug fixed
in one was missed in the others. Centralize it in a single helper.
```

*Example 3 (docs, no body):*

Input: fixed a typo in the README install command.

```
docs: fix install command typo in README
```

The subject is self-explanatory, so the body is omitted.

## External references

- Conventional Commits 1.0.0: https://www.conventionalcommits.org/en/v1.0.0/
- Linux kernel SubmittingPatches: https://www.kernel.org/doc/html/latest/process/submitting-patches.html
- Tim Pope, A Note About Git Commit Messages: https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html
- Chris Beams, How to Write a Git Commit Message: https://cbea.ms/git-commit/

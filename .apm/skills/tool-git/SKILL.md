---
name: tool-git
description: >-
  Slice the working tree into atomic commits and create them locally with no
  in-chat approval gates. The user reviews each commit's message and diff in
  their own Git tool afterward and can ask to redo any commit; nothing is ever
  pushed, so every commit is safe to undo. Conventional Commits subject with a
  why-focused body. Use when turning working-tree changes into one or more
  clean, atomic commits.
when_to_use: >-
  User says "let's commit", "commit this", "stage and commit", "draft a commit",
  "slice the WIP", or wants to turn working-tree changes into atomic commits.
---

# tool-git

Slice the working tree into atomic commits and create them locally, then hand
off for review. There are no in-chat approval gates: slicing, staging, the
message, and the commit all happen without pausing. The user reviews each
commit's message and diff in their own Git tool and asks to redo any that are
wrong. The user pushes manually; this skill never pushes.

## Principles

- One commit is one logical change.
- No approval gates. Slice, stage, write the message, and commit without
  pausing. Review happens afterward, in the user's Git tool.
- Because the skill never pushes, every commit it makes is local and safe to
  undo. A redo request resets the target commit and rebuilds it.
- Subject follows Conventional Commits. Body is Linus-style: imperative, wrapped
  at 72, and explains *why* rather than *what*.
- Never push. Amend or reset only when redoing on request, or when the user asks.

## When not to use

- Pushing to a remote: out of scope.
- Reworking commits that are already pushed: out of scope, since the redo path
  rewrites local history.

## Steps

### 1. Survey the working tree

Run in parallel:

- `git status --short`
- `git diff --stat` (unstaged)
- `git diff --stat --staged` (already staged)
- `git log --oneline -5` (recent style reference)

If the index already holds staged changes, run `git reset` to unstage them so
slicing starts from a clean index, and say so in one line. Nothing is lost: the
changes stay in the working tree and get re-sliced below.

### 2. Slice, stage, and commit each logical change

Work through the whole tree, one atomic commit per logical change, looping until
`git status` is clean. For each:

1. Pick one logical slice (slicing rules below).
2. Stage it (staging commands below).
3. Draft the message (message rules below).
4. Commit it (commit command below). On a pre-commit hook failure, surface the
   output verbatim, fix the underlying issue, re-stage, and commit again. Never
   use `--no-verify`.

Keep a running note of each commit as `<short-hash> <subject>` for the hand-off.

**Slicing rules:**

- Single responsibility. Do not mix functional changes with formatting unless
  they are tightly coupled.
- Prefer whole-file staging. Use hunks only when one file holds two concerns.
- When in doubt, prefer smaller commits. Target five files or fewer; more
  usually means the slice is too coarse.

**Staging:**

- Whole file, add or modify: `git add <path>`.
- Whole file, delete: `git rm <path>`.
- Hunks: write the diff to a patch file, keep only the wanted hunks, then
  `git apply --cached <patch>`.

**Message rules.** Format:

```
<type>(<scope>): <subject>

<body: why, wrapped at 72 chars per line>

<footer: optional Refs/Closes/BREAKING CHANGE>
```

*Subject*: imperative mood ("add", not "added"); 72 chars or fewer, target 50;
no trailing period; do not repeat the type in the text.

*Type*: pick exactly one Conventional Commits type. If two fit, the slice is too
broad: split it. Common types are `feat`, `fix`, `refactor`, `perf`, `docs`,
`test`, `build`, `ci`, `chore`, `style`, `revert`. For the full table and the
common confusions (such as `refactor` vs `style`), LOAD
[references/commit-message-style.md](references/commit-message-style.md).

*Scope* (optional): a short component name from this repo. Drop it if the change
is project-wide.

*Body*: explain motivation and trade-offs; the diff already shows what changed.
Imperative voice, no "I", "we", or "this commit", and no marketing prose. Prose
over bullets. Omit the body entirely when the subject is self-explanatory.

*Footer* (optional): `Refs: #123`, `Closes: #456`, or `BREAKING CHANGE: <desc>`.

*Trailers*: no AI co-authorship trailers. The user is the author. Add one only
if the user explicitly asks.

**Commit command.** Use a heredoc to preserve formatting:

```bash
git commit -m "$(cat <<'EOF'
<type>(<scope>): <subject>

<body>

<footer>
EOF
)"
```

### 3. Hand off for review

List every commit made, in order, as `<short-hash> <subject>`. Tell the user to
review the messages and diffs in their own Git tool, that nothing was pushed,
and that they can ask to redo any commit (see below). Then stop.

### 4. Redo on request

A redo request names a commit (or "the last one"). The commit is unpushed, so it
is safe to rewrite. Pick the smallest operation:

- *Message only, tip commit*: `git commit --amend` with the corrected message.
- *Slice or content wrong, tip commit*: `git reset --soft HEAD~1` to return its
  changes to the index, adjust the staging, then recommit. Use `git reset HEAD~1`
  instead if the slice needs regrouping from scratch.
- *An earlier commit*: redoing it means replaying the commits after it. Reset to
  just before the target (`git reset --soft <hash>~1`), rebuild the target, then
  recreate the later commits. Confirm the plan with the user first, since this
  rewrites more than one commit.

After any redo, run `git show HEAD --stat` (or `git log --oneline`) and report
the new state in one line.

## Anti-patterns

- Pausing for in-chat approval before committing: review happens afterward, in
  the user's Git tool.
- Mixing unrelated concerns in one commit.
- A body that restates *what* changed instead of *why*.
- Trailing period in the subject, or a past-tense subject ("added X").
- Marketing tone in the body.
- AI co-authorship trailers, unless the user asks.
- `--no-verify`: fix the hook failure instead.
- Rewriting pushed commits, or pushing: not this skill's job.

For the full type table, worked message examples, and external references, LOAD
[references/commit-message-style.md](references/commit-message-style.md).

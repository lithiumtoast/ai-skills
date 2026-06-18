---
name: tool-git
description: >-
  Stage one atomic slice of the working tree and create a local commit after
  explicit approval. Two gates: slice approval, then message approval.
  Conventional Commits subject with a why-focused body. Does not push. Use when
  turning working-tree changes into one or more clean, atomic commits.
when_to_use: >-
  User says "let's commit", "commit this", "stage and commit", "draft a commit",
  "slice the WIP", or wants to turn working-tree changes into atomic commits.
---

# tool-git

Produce one atomic commit at a time, with manual approval at every gate. The
user pushes manually; this skill never pushes.

## Principles

- One commit is one logical change.
- Two approval gates: the slice, then the message. Do not advance past a gate
  without explicit user approval. The user verifies the staged diff in their own
  Git tool.
- Subject follows Conventional Commits. Body is Linus-style: imperative, wrapped
  at 72, and explains *why* rather than *what*.
- Never push. Never amend an existing commit unless the user says so.

## When not to use

- Pushing to a remote: out of scope.
- Bulk-committing many unrelated changes at once: slice first; this skill makes
  one commit per invocation.

## Steps

### 1. Survey the working tree

Run in parallel:

- `git status --short`
- `git diff --stat` (unstaged)
- `git diff --stat --staged` (already staged)
- `git log --oneline -5` (recent style reference)

If the index already has staged changes, ask whether to use them as the slice or
`git reset` and start fresh. Wait for the answer.

### 2. Propose one slice (Gate 1)

Identify a single logical change and output a proposal:

```
Slice: <one-line description>

Files (whole-file stage):
- <path>: <add | modify | delete>: <one-line rationale>

Hunks (partial stage):
- <path>:<lineRange>: <one-line rationale>
```

Slicing rules:

- Single responsibility. Do not mix functional changes with formatting unless
  they are tightly coupled.
- Prefer whole-file staging. Use hunks only when one file holds two concerns.
- When in doubt, prefer smaller commits. Target five files or fewer; more
  usually means the slice is too coarse.

Do not list the files you are leaving out; `git status` already shows them.

Wait for `approve`, `modify` (user names the changes), or `reject` (start over).
Do not stage until approved.

### 3. Stage the approved slice

Whole files:

- Add or modify: `git add <path>`.
- Delete: `git rm <path>`.

Hunks (only if approved):

- Write the diff to a patch file, keep only the approved hunks, then
  `git apply --cached <patch>`. Or ask the user to run `git add -p <path>`
  interactively and tell you when done.

State in one line what was staged so the user can verify it in their own Git
tool. Do not run a full staged-diff review; the user checks the staged changes
before approving the message.

### 4. Draft the commit message (Gate 2)

Format:

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

Show the drafted message. Ask for `approve`, `edit` (user provides changes), or
`cancel`. Do not commit until approved.

### 5. Commit

Use a heredoc to preserve formatting:

```bash
git commit -m "$(cat <<'EOF'
<type>(<scope>): <subject>

<body>

<footer>
EOF
)"
```

If a pre-commit hook fails, surface its output verbatim, fix the underlying
issue, re-stage, and commit again. Never use `--no-verify`.

Then run `git show HEAD --stat` and confirm the commit matches the approved
slice and message.

### 6. Hand off

Report the commit hash and subject in one line, and that the user pushes
manually. If `git status` still shows changes, ask whether to start the next
slice (return to step 1).

## Anti-patterns

- Committing without slice or message approval.
- Mixing unrelated concerns in one commit.
- A body that restates *what* changed instead of *why*.
- Trailing period in the subject, or a past-tense subject ("added X").
- Marketing tone in the body.
- AI co-authorship trailers, unless the user asks.
- `--no-verify`: fix the hook failure instead.
- Pushing: not this skill's job.

For the full type table, worked message examples, and external references, LOAD
[references/commit-message-style.md](references/commit-message-style.md).

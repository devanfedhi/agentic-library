---
name: commit
description: Draft and create Git commits with conventional scoped subject lines, detailed bullet-point bodies, and explicit user approval before committing. Use when the user asks Codex to create a commit, draft a commit message, choose a conventional commit type or scope, commit current changes with optional user-provided type/scope/message, or push after committing.
---

# Commit Message

## Overview

Create concise Git commit messages in this format:

```text
type(scope): commit message

- detail about the change
- another detail about the change
```

Use conventional commit types such as `feat`, `fix`, `chore`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `cd`, and `revert`. Use a short lowercase scope that names the main area changing, such as `api`, `scraper`, `ci`, `cd`, `db`, `deps`, `config`, `ui`, `auth`, `tests`, or another repo-specific subsystem.

## Workflow

1. Inspect the Git state before committing.
   - Use `git status --short`.
   - Use `git diff --stat`, `git diff --staged --stat`, and focused diffs as needed.
   - Treat all current tracked, untracked, staged, and unstaged changes as the intended commit unless the user explicitly asks for a narrower pathspec.
   - If the working tree is clean, say there is nothing to commit.

2. Gather fields.
   - If the user provides `type`, `scope`, and message, preserve them except for light formatting.
   - If a field is missing, infer it from the changes.
   - If the changes are ambiguous or combine unrelated work, ask before committing or suggest splitting commits.

3. Format the complete message.
   - Use `type(scope): commit message`.
   - Keep `type` and `scope` lowercase.
   - Use an imperative, concise message with no trailing period.
   - Keep the first line preferably under 72 characters when practical.
   - Add a blank line after the first line, followed by 2–4 bullet points.
   - Start each bullet with `- ` and describe a substantive implementation detail, behavior change, test, migration, or configuration change.
   - Keep bullets concise, specific, and free of trailing periods.

4. Request explicit confirmation.
   - Show the exact complete commit message, including all bullet points.
   - Ask the user to confirm that message before staging or committing.
   - Do not run `git add` or `git commit` until the user explicitly approves the proposed message.
   - If the user requests edits, revise the message and ask for confirmation again.

5. Commit only after confirmation.
   - Stage all unstaged and untracked changes before committing; do not ask for separate permission just to stage.
   - Use `git add -A` by default, or `git add -A -- <pathspec>` when the user explicitly limits the commit to specific files or directories.
   - Re-check `git status --short` and `git diff --staged --stat` after staging so the committed set is visible before the commit.
   - If the staged changes materially differ from the changes used to draft the approved message, stop, update the message, and request confirmation again.
   - Pass the approved subject and bullet-point body to `git commit` without changing them.

6. Push only when requested with a push flag.
   - Accept `--push`, `push`, `push: true`, or natural language such as "commit and push" as a request to push after committing.
   - After a successful commit, run `git push`.
   - If the branch has no upstream, use `git push -u origin HEAD`.
   - If the push fails, report the failure and leave the commit in place.

## Inference Rules

Choose `type` from the main intent:

- `feat`: adds user-visible behavior or a new capability.
- `fix`: corrects broken behavior.
- `chore`: maintenance that does not change behavior.
- `docs`: documentation only.
- `test`: tests only or test infrastructure.
- `refactor`: restructures code without intended behavior change.
- `perf`: performance improvement.
- `build`: build system, packaging, lockfiles, or dependencies.
- `ci`: continuous integration workflow changes.
- `cd`: deployment or release delivery changes.
- `style`: formatting-only code changes.

Choose `scope` from the dominant changed path, package, subsystem, or config area. Prefer existing repo vocabulary over generic scopes. Examples:

- `.github/workflows/*` -> `ci`
- deploy scripts, release config, hosting config -> `cd`
- scraper modules, crawl/extract/parsing logic -> `scraper`
- API routes, clients, schemas, integrations -> `api`
- tests, fixtures, snapshots -> `tests`
- package manager files or dependency manifests -> `deps`
- config files -> `config`
- database migrations or models -> `db`

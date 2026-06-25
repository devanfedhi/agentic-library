---
name: commit
description: Create Git commits with conventional scoped messages in the exact format `type(scope): commit message`. Use when the user asks Codex to create a commit, draft a commit message, choose a conventional commit type or scope, or commit current changes with optional user-provided type, scope, and message.
---

# Commit Message

## Overview

Create concise Git commit messages in this exact format:

```text
type(scope): commit message
```

Use conventional commit types such as `feat`, `fix`, `chore`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `cd`, and `revert`. Use a short lowercase scope that names the main area changing, such as `api`, `scraper`, `ci`, `cd`, `db`, `deps`, `config`, `ui`, `auth`, `tests`, or another repo-specific subsystem.

## Workflow

1. Inspect the Git state before committing.
   - Use `git status --short`.
   - Use `git diff --stat`, `git diff --staged --stat`, and focused diffs as needed.
   - If staged changes exist, treat the staged set as the intended commit unless the user asks otherwise.

2. Gather fields.
   - If the user provides `type`, `scope`, and message, preserve them except for light formatting.
   - If a field is missing, infer it from the changes.
   - If the changes are ambiguous or combine unrelated work, ask before committing or suggest splitting commits.

3. Format exactly.
   - Use `type(scope): commit message`.
   - Keep `type` and `scope` lowercase.
   - Use an imperative, concise message with no trailing period.
   - Keep the first line preferably under 72 characters when practical.

4. Commit only when the user requested a commit.
   - Show or mention the selected message before running `git commit` when practical.
   - Do not stage unrelated files automatically unless the user explicitly asks.
   - If staged changes exist, commit only the staged changes unless the user asks to stage more.
   - If nothing is staged and the user clearly asks to commit all current changes, stage the relevant files and commit them.

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

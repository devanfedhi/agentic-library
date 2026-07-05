---
name: pr-create
description: Review a feature branch's commit messages and code changes, collect and verify its related issue, draft a concise conventional pull request from a template, request explicit user confirmation, and create it with the GitHub CLI. Use when the user asks Codex to review recent branch changes and open, create, or draft a GitHub pull request.
---

# Create Pull Request

Review every commit and code change on the current feature branch, then preview and create a pull request. Never create the pull request before the user approves the exact preview.

Before drafting, read [PULL_REQUEST_TEMPLATE.md](references/PULL_REQUEST_TEMPLATE.md) and use it for the title and body guidance.

## Workflow

1. Gather the issue and branch context.
   - If the skill was invoked with an issue reference, use it first and ask only for confirmation when needed.
   - Otherwise ask the user, `What issue number should this pull request close?`
   - Accept a plain number, `#<number>`, a GitHub issue URL, or a GitHub project-item URL containing an issue reference.
   - For project-item URLs, decode an `issue=<owner>|<repo>|<number>` query value and use its final component as the issue number. For example, `issue=devanfedhi%7Coz-scraper%7C1` means issue `#1` in `devanfedhi/oz-scraper`.
   - If the user already supplied an issue reference, normalize it and ask them to confirm the issue number instead of asking again.
   - Verify the issue belongs to the current repository and is open with `gh issue view` before drafting the pull request.
   - Inspect `git status --short` and identify the current branch.
   - Use the user's requested base branch. Otherwise, determine the repository's default branch from Git or `gh repo view`.
   - Stop if the current branch is detached, is the base branch, or has no commits beyond the base.
   - Report uncommitted changes because they are not included in the pull request.
   - Check for an existing open pull request from the same head branch and avoid creating a duplicate.
   - Keep the workflow suitable for a one-developer project: do not ask for reviewers, assignees, or team approvals.

2. Review the branch before drafting.
   - Inspect the commits with `git log <base>..HEAD`, including their full messages.
   - Check whether each commit message accurately represents its changes and follows the repository's conventions.
   - Inspect `git diff --stat <base>...HEAD`, `git diff --check <base>...HEAD`, and the full relevant diff.
   - Review for correctness, regressions, security problems, missing tests, and unintended changes. Run focused tests or validation when practical.
   - Report findings with file and line references. Stop before drafting the pull request if a material issue should be fixed first.

3. Draft and preview the pull request.
   - Infer the title from the aggregate branch changes and commit history, not mechanically from one commit.
   - Follow the title and body guidance in `references/PULL_REQUEST_TEMPLATE.md`.
   - Use the confirmed issue number and only the validation actually performed.
   - Preserve the template's headings, and do not leave comments or prompt bullets in the rendered description.
   - Show the exact base branch, head branch, title, and complete Markdown description.
   - State whether the feature branch must be pushed before creating the pull request.
   - Ask the user to explicitly confirm the complete preview.
   - If the user requests edits, revise the preview and ask for confirmation again.

4. Create the pull request only after confirmation.
   - Re-check the branch, `HEAD`, and reviewed diff before acting.
   - If they changed after confirmation, regenerate the preview and request confirmation again.
   - If the approved preview disclosed a required push, push the branch with its existing upstream or use `git push -u origin HEAD`.
   - Run `gh pr create --base <base> --head <head> --title <title> --body <rendered-description>` using the approved values without changing them.
   - Return the pull request URL. If creation fails, report the error without silently retrying with different content.

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

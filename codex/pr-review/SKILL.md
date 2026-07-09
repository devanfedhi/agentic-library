---
name: pr-review
description: Review an existing GitHub pull request, report findings first, and optionally leave PR comments with the GitHub CLI after explicit user approval. Use when the user asks Codex to review a PR, inspect a pull request diff, summarize review findings, or post review comments back to GitHub.
---

# Review Pull Request

Review the pull request like a code reviewer first. Findings come before summaries. Do not post anything to GitHub until the user approves the exact review action and comment content.

## Workflow

1. Gather pull request context.
   - Accept a PR number, URL, or current branch PR.
   - Inspect the PR with `gh pr view` and collect the base branch, head branch, title, body, state, author, and changed files.
   - Load the diff with `gh pr diff` and inspect the relevant local files when needed.
   - If `gh` cannot resolve the PR or auth is missing, stop and report that clearly.

2. Review before proposing actions.
   - Prioritize correctness bugs, regressions, security issues, missing tests, and risky edge cases.
   - Use the repo's code review style: list findings first, ordered by severity, with file and line references when possible.
   - Keep the summary brief and secondary.
   - If no findings are discovered, say that explicitly and mention any residual risk or missing validation.

3. Decide whether GitHub mutation is needed.
   - If the user only asked for a review, stop after presenting findings.
   - If the user asked to leave comments on the PR, prepare the exact action but do not execute it yet.
   - Support comment-only GitHub write paths:
     - A review-level comment via `gh pr review --comment`.
     - A top-level PR conversation comment via `gh pr comment`.
     - Direct inline PR comments on specific files/lines via `gh api`.
   - Prefer inline comments whenever a finding maps cleanly to a changed file and line.
   - Fall back to a review-level or top-level comment only when inline anchoring would be misleading or impractical.

4. Prepare a preview for any GitHub write action.
   - Show the exact comment plan to be taken: inline comments, a review-level comment, a top-level PR comment, or a combination when needed.
   - Show the exact comment bodies before posting them.
   - For inline comments, include each proposed comment with its file path and target line.
   - Ask for explicit user approval before sending anything to GitHub.

5. Post only after confirmation.
   - Re-check that the PR head SHA and reviewed diff have not changed.
   - Use `gh pr review --comment` for a single review-level comment.
   - Use `gh pr comment` for a top-level PR conversation comment.
   - Use `gh api` for direct inline comments on specific lines. Prefer creating a pending review with inline comments and then submitting it so related comments stay grouped.
   - Return the posted review URL or confirm that the comments were submitted.

## Inline Comment Guidance

- `gh pr review` and `gh pr comment` do not cover file-line inline review comments.
- For direct PR line comments, use `gh api` against GitHub's pull request review endpoints.
- Prefer review-based inline comments over standalone inline comments so multiple findings can be submitted together.
- Each inline comment must be anchored to the current PR diff using the latest head SHA plus the file path and diff position or line/side fields required by the endpoint.
- If the diff moved after review, regenerate the anchors instead of posting stale comments.

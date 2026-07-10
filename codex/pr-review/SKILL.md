---
name: pr-review
description: Review an existing GitHub pull request, report findings first, and optionally leave PR comments with the GitHub CLI after explicit user approval. Use when the user asks Codex to review a PR, inspect a pull request diff, summarize review findings, or post review comments back to GitHub.
---

# Review Pull Request

Review the pull request like a code reviewer first. Findings come before summaries. Do not post anything to GitHub until the user approves the exact review action and comment content.

Be intentionally thorough. Do not stop after the first one or two plausible findings if the PR still has uncovered risk areas. Inspect the changed code, then expand into the surrounding repository context before concluding the review.

## Workflow

1. Gather pull request context.
   - Accept a PR number, URL, or current branch PR.
   - Inspect the PR with `gh pr view` and collect the base branch, head branch, title, body, state, author, and changed files.
   - Load the diff with `gh pr diff` and inspect the relevant local files when needed.
   - Build a quick review map of the changed files, grouped by subsystem or package, so the review covers the main surfaces instead of only one hotspot.
   - If `gh` cannot resolve the PR or auth is missing, stop and report that clearly.

2. Review before proposing actions.
   - Prioritize correctness bugs, regressions, security issues, missing tests, and risky edge cases.
   - Read beyond the diff when needed to understand impact:
     - inspect nearby functions, types, tests, fixtures, configuration, and documentation touched by the change
     - trace important symbols into callers, callees, and shared utilities
     - compare the new behavior against existing repo patterns instead of reviewing the patch in isolation
   - Cover the main risk buckets explicitly: behavior correctness, error handling, data flow, state changes, compatibility, tests, and unintended side effects.
   - Make at least one deliberate second pass after the initial findings list to check for additional issues in untouched parts of the diff or surrounding code.
   - Do not pad the review with weak nitpicks just to increase comment count, but do keep looking until the significant changed areas have been examined.
   - Use the repo's code review style: list findings first, ordered by severity, with file and line references when possible.
   - Prefer reporting multiple distinct findings when the PR genuinely has them, rather than collapsing everything into one broad comment.
   - Keep the summary brief and secondary.
   - If no findings are discovered, say that explicitly and mention any residual risk or missing validation.

3. Decide whether GitHub mutation is needed.
   - If the user only asked for a review, stop after presenting findings.
   - If the user asked to leave comments on the PR, prepare the exact action but do not execute it yet.
   - Default to direct inline PR comments on specific files and lines via `gh api`.
   - Treat inline comments as the normal path for nearly every finding.
   - Use a review-level comment via `gh pr review --comment` only when the feedback truly applies to the PR as a whole and cannot be anchored honestly to one changed line.
   - Use a top-level PR conversation comment via `gh pr comment` only as a last resort when neither inline nor review-level placement fits.
   - If a finding maps to a changed file and line, post it inline rather than bundling it into a general review comment.

4. Prepare a preview for any GitHub write action.
   - Show the exact comment plan to be taken, with inline comments as the default and any non-inline comment justified briefly.
   - Show the exact comment bodies before posting them.
   - For inline comments, include each proposed comment with its file path and target line.
   - Ask for explicit user approval before sending anything to GitHub.

5. Post only after confirmation.
   - Re-check that the PR head SHA and reviewed diff have not changed.
   - Use `gh api` for direct inline comments on specific lines by default. Prefer creating a pending review with inline comments and then submitting it so related comments stay grouped.
   - Use `gh pr review --comment` only for rare PR-level feedback that does not belong on a specific changed line.
   - Use `gh pr comment` only for the rare fallback case where neither inline nor review-level placement fits.
   - Return the posted review URL or confirm that the comments were submitted.

## Inline Comment Guidance

- `gh pr review` and `gh pr comment` do not cover file-line inline review comments.
- For direct PR line comments, use `gh api` against GitHub's pull request review endpoints.
- Inline comments should be the default output for almost all findings.
- Prefer review-based inline comments over standalone inline comments so multiple findings can be submitted together.
- Do not collapse line-specific findings into one broad review comment just for convenience.
- When considering a non-inline comment, first ask whether the concern can be anchored to a representative changed line without being misleading. If yes, keep it inline.
- Each inline comment must be anchored to the current PR diff using the latest head SHA plus the file path and diff position or line/side fields required by the endpoint.
- If the diff moved after review, regenerate the anchors instead of posting stale comments.

# Technical Specification — Issue #9

## 1. Issue Overview

| Field | Value |
|---|---|
| Title | Add Claude Code GitHub Workflow |
| Description | Adds GitHub Actions workflow enabling Claude Code integration — `@claude` mentions in PR/issue comments trigger an agent run with repo context (files, diffs, prior comments). |
| Labels | none |
| Priority | N/A — already merged |
| State | MERGED (as PR, filed under Issues API) |

## 2. Problem Analysis

Not a bug. Issue 9 is the PR that introduced `.github/workflows/claude.yml`, already merged into `main`. Repo currently contains:

- `.github/workflows/claude.yml` — the workflow this issue added. Triggers on `issue_comment`, `pull_request_review_comment`, `issues` (opened/assigned), `pull_request_review`, gated by `contains(..., '@claude')`. Runs `anthropics/claude-code-action@v1` with `CLAUDE_CODE_OAUTH_TOKEN` secret.
- `.github/workflows/claude-code-review.yml` — a related, separate workflow (added later, in #8) for automated PR review. Currently has **uncommitted staged changes** on `skills` branch: prompt block toggled from single-line `/code-review:code-review` slash-command invocation back to a verbose inline multi-criteria prompt (commented single-line version preserved above it). Inconsistent leading whitespace on several re-added lines (`  #   ` vs `  #  `).

No reproduction steps, no discussion comments, no root cause to trace — issue is historical record of already-shipped infra.

## 3. Proposed Solution

No code change required to satisfy issue 9 itself (already merged). Two adjacent, optional follow-ups visible in working tree, not part of issue 9's scope:

1. Decide and commit the staged `claude-code-review.yml` prompt change (slash-command vs. inline prompt) — currently uncommitted, indentation is inconsistent.
2. No changes needed to `claude.yml` — matches merged state, no diff pending.

Recommend: keep this spec as documentation-only; do not bundle the unrelated staged workflow edit into an issue-9 deliverable.

## 4. Step-by-Step Implementation

1. **No implementation needed for issue 9** — workflow already exists and merged.
2. *(Optional, separate from issue 9)* Commit or revert staged `claude-code-review.yml` changes on `skills` branch with its own conventional commit (`chore:` or `refactor:`).
3. *(Optional)* Fix inconsistent comment-line indentation in the commented-out prompt block if kept.

## 5. Verification Strategy

### Unit Tests
- N/A — YAML workflow config, no unit-testable logic in-repo.

### Integration Tests
- N/A — GitHub Actions execution is out of local test scope.

### Manual Checks
- Confirm `@claude` mention in a PR/issue comment triggers `claude.yml` job (already verified by merge).
- If prompt in `claude-code-review.yml` is changed: open a test PR, confirm Claude posts review comment via `gh pr comment` matching new prompt instructions.

## 6. Files to Modify

| File Path | Nature of Change |
|---|---|
| none required | issue 9 already resolved |

## 7. New Files to Create

| File Path | Purpose |
|---|---|
| none | — |

## 8. Existing Utilities to Leverage

| Utility | Benefit |
|---|---|
| `anthropics/claude-code-action@v1` | Already integrated, handles auth + agent execution |
| `CLAUDE_CODE_OAUTH_TOKEN` secret | Already configured per issue body |

## 9. Acceptance Criteria

- [x] Workflow file present and merged (`claude.yml`)
- [x] `@claude` mention triggers workflow (functional per merge)
- [ ] N/A — no new tests needed, no regression risk from issue 9 itself

## 10. Out of Scope

- Staged uncommitted changes to `claude-code-review.yml` (separate concern, not introduced by issue 9)
- Any redesign of trigger conditions or permissions in either workflow file

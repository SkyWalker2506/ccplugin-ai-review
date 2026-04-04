---
description: Run AI code review on the current GitHub PR using free OpenRouter models
allowed-tools: [Bash, Read, Glob]
---

# AI PR Review

Trigger an automated AI code review on the current or specified pull request.

## Instructions

1. Check if `gh` is available and user is in a git repo: `gh pr view 2>/dev/null`
2. If a PR exists, check if the GitHub Actions workflow `.github/workflows/ai-review.yml` exists
3. If the workflow exists, trigger it: `gh workflow run ai-review.yml`
4. If no workflow, run the review directly:
   - Get PR diff: `gh pr diff`
   - Send to OpenRouter (Gemma 3 27B free) for review
   - Post result as PR comment: `gh pr comment --body "<review>"`
5. Report the review result or workflow trigger status to the user

If `OPENROUTER_API_KEY` is not set in environment, instruct user to add it to `~/.claude/secrets/secrets.env`.

## Arguments

`$ARGUMENTS` may contain a PR number. If provided, use `gh pr view $ARGUMENTS`.

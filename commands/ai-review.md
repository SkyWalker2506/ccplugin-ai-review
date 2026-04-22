---
description: Run AI code review on the current GitHub PR using free OpenRouter models
allowed-tools: [Bash, Read, Glob]
---

# AI PR Review

Trigger an automated AI code review on the current or specified pull request.

## Instructions

### Step 1 — Validate environment

1. Check `gh` is available: run `gh --version`. If missing, tell the user to install GitHub CLI and stop.
2. Check the current directory is a git repo: run `git rev-parse --is-inside-work-tree 2>/dev/null`. If not, tell the user and stop.
3. Check `OPENROUTER_API_KEY` is set in the environment. If not set:
   - Print: "OPENROUTER_API_KEY is not set. Add it to ~/.claude/secrets/secrets.env:\n  OPENROUTER_API_KEY=<your key from openrouter.ai>"
   - Stop.

### Step 2 — Resolve the PR

If `$ARGUMENTS` is provided:
- Validate it is a positive integer. If not numeric, print: "Invalid PR number: '$ARGUMENTS'. Usage: /ai-review <PR_number>" and stop.
- Use `gh pr view $ARGUMENTS` to confirm the PR exists.

If no argument:
- Use `gh pr view` to get the current branch's PR.
- If no PR exists, tell the user to open a PR first and stop.

### Step 3 — Get the diff

Run `gh pr diff <PR_NUMBER>`.
- If the diff is empty, print: "No changes found in this PR — nothing to review." and stop.
- Truncate the diff to 8000 characters if it is longer. Append a note: _(diff truncated at 8000 chars)_ after truncating.

### Step 4 — Send to OpenRouter

Build the review prompt:
```
You are a senior software engineer reviewing a GitHub pull request.

PR #<NUMBER>: <TITLE>

Diff:
<DIFF>

Provide a structured review with these exact sections:
## Summary
Brief overview of what this PR does.

## Issues
List any bugs, security concerns, or correctness problems. If none, write 'No issues found.'

## Suggestions
Actionable improvement suggestions (style, performance, maintainability). If none, write 'Looks good.'

Be concise and specific. Reference line numbers where possible.
```

Call OpenRouter API using Bash:
```bash
curl -s -X POST https://openrouter.ai/api/v1/chat/completions \
  -H "Authorization: Bearer $OPENROUTER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"google/gemma-3-27b-it:free","messages":[{"role":"user","content":"<PROMPT>"}],"max_tokens":1024}'
```

If the primary model (`google/gemma-3-27b-it:free`) returns an error or is unavailable, retry with the fallback model `mistralai/mistral-7b-instruct:free`.

If both fail, print: "OpenRouter API is unavailable. Check your API key or try again later." and stop.

### Step 5 — Post the review

Post the review as a PR comment using:
```bash
gh pr comment <PR_NUMBER> --body "<REVIEW>"
```

The comment should be prefixed with:
```
## 🤖 AI Code Review
```

And suffixed with:
```
---
_Powered by [ccplugin-ai-review](https://github.com/SkyWalker2506/ccplugin-ai-review) · Free via OpenRouter_
```

### Step 6 — Report result

Tell the user the review was posted and provide the PR URL.

## Arguments

`$ARGUMENTS` may contain a PR number (positive integer). If provided, use `gh pr view $ARGUMENTS`.
If not provided, review the current branch's open PR.

## Models

| Role | Model |
|------|-------|
| Primary | `google/gemma-3-27b-it:free` |
| Fallback | `mistralai/mistral-7b-instruct:free` |

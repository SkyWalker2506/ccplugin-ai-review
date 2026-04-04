---
name: ai-review
description: This skill should be used when the user asks to "review a PR", "code review", "AI review", "check the pull request", or asks about automated review via OpenRouter or Gemma. Also activates when setting up GitHub Actions for PR review.
version: 1.0.0
---

# AI PR Review Skill

Automated pull request review using OpenRouter free models.

## How It Works

1. GitHub Action triggers on every PR
2. Gets the PR diff via `gh pr diff`
3. Sends to OpenRouter (Gemma 3 27B Free) for analysis
4. Posts review as PR comment

## Setup

### Requirements
- `OPENROUTER_API_KEY` in `~/.claude/secrets/secrets.env`
- `GH_TOKEN` or `GITHUB_TOKEN` with PR write permissions
- `gh` CLI installed

### GitHub Action Setup

Add `.github/workflows/ai-review.yml` to your repo:

```yaml
name: AI Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: AI Review
        env:
          OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          PR_DIFF=$(gh pr diff)
          REVIEW=$(curl -s https://openrouter.ai/api/v1/chat/completions \
            -H "Authorization: Bearer $OPENROUTER_API_KEY" \
            -H "Content-Type: application/json" \
            -d "{\"model\":\"google/gemma-3-27b-it:free\",\"messages\":[{\"role\":\"user\",\"content\":\"Review this PR diff:\n\n$PR_DIFF\n\nProvide actionable feedback.\"}]}" \
            | python3 -c "import json,sys; print(json.load(sys.stdin)['choices'][0]['message']['content'])")
          gh pr comment --body "$REVIEW"
```

## Cost

$0 — uses OpenRouter free tier (Gemma 3 27B Free).

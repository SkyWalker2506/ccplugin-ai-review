---
name: ai-review
description: This skill should be used when the user asks to "review a PR", "code review", "AI review", "check the pull request", or asks about automated review via OpenRouter or Gemma. Also activates when setting up GitHub Actions for PR review.
version: 1.1.0
---

# AI PR Review Skill

Automated pull request review using OpenRouter free models.

## How It Works

1. GitHub Action triggers on every PR open or sync
2. Gets the PR diff via `gh pr diff` (truncated at 8000 chars)
3. Sends to OpenRouter (primary: Gemma 3 27B Free, fallback: Mistral 7B Free) for analysis
4. Posts structured review as PR comment (Summary / Issues / Suggestions)

## Models

| Role | Model ID | Notes |
|------|----------|-------|
| Primary | `google/gemma-3-27b-it:free` | Best quality, try first |
| Fallback | `mistralai/mistral-7b-instruct:free` | Used if primary is at capacity |

Both models are free-tier on OpenRouter. If both are unavailable, the workflow exits with a clear error message.

## Setup

### Requirements
- `OPENROUTER_API_KEY` in `~/.claude/secrets/secrets.env`
- `GH_TOKEN` or `GITHUB_TOKEN` with PR write permissions
- `gh` CLI installed

### GitHub Action Setup

Add `.github/workflows/ai-review.yml` to your repo (also available in this plugin repo):

```yaml
name: AI Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/checkout@v4

      - name: Validate API key
        run: |
          if [ -z "$OPENROUTER_API_KEY" ]; then
            echo "::error::OPENROUTER_API_KEY secret is not set."
            exit 1
          fi
        env:
          OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}

      - name: Get PR diff
        id: diff
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          PR_DIFF=$(gh pr diff ${{ github.event.pull_request.number }} 2>/dev/null || echo "")
          if [ -z "$PR_DIFF" ]; then
            echo "skip=true" >> "$GITHUB_OUTPUT"
            exit 0
          fi
          PR_DIFF_TRUNCATED="${PR_DIFF:0:8000}"
          { echo "diff<<EOF_DIFF"; echo "$PR_DIFF_TRUNCATED"; echo "EOF_DIFF"; } >> "$GITHUB_OUTPUT"
          echo "skip=false" >> "$GITHUB_OUTPUT"

      - name: AI Review via OpenRouter
        if: steps.diff.outputs.skip == 'false'
        env:
          OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          PR_DIFF: ${{ steps.diff.outputs.diff }}
          PR_NUMBER: ${{ github.event.pull_request.number }}
          PR_TITLE: ${{ github.event.pull_request.title }}
        run: |
          PRIMARY_MODEL="google/gemma-3-27b-it:free"
          FALLBACK_MODEL="mistralai/mistral-7b-instruct:free"

          PROMPT="Review PR #${PR_NUMBER}: ${PR_TITLE}\n\nDiff:\n${PR_DIFF}\n\nProvide:\n## Summary\n## Issues\n## Suggestions"

          for MODEL in "$PRIMARY_MODEL" "$FALLBACK_MODEL"; do
            PAYLOAD=$(python3 -c "
          import json,sys
          print(json.dumps({'model':'$MODEL','messages':[{'role':'user','content':sys.stdin.read()}],'max_tokens':1024}))" <<< "$PROMPT")
            RESPONSE=$(curl -s -w "\n%{http_code}" \
              -X POST https://openrouter.ai/api/v1/chat/completions \
              -H "Authorization: Bearer $OPENROUTER_API_KEY" \
              -H "Content-Type: application/json" \
              -d "$PAYLOAD")
            HTTP_CODE=$(echo "$RESPONSE" | tail -n1)
            BODY=$(echo "$RESPONSE" | head -n -1)
            if [ "$HTTP_CODE" = "200" ]; then
              REVIEW=$(echo "$BODY" | python3 -c "import json,sys; print(json.load(sys.stdin)['choices'][0]['message']['content'])")
              gh pr comment "$PR_NUMBER" --body "## 🤖 AI Code Review\n\n${REVIEW}\n\n---\n_Powered by ccplugin-ai-review · Free via OpenRouter_"
              exit 0
            fi
            echo "Model $MODEL returned HTTP $HTTP_CODE, trying next..."
          done
          echo "::error::All OpenRouter models unavailable."
          exit 1
```

## Cost

$0 — uses OpenRouter free tier (Gemma 3 27B Free, Mistral 7B Free fallback).

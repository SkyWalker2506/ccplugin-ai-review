# ai-review — Claude Code Plugin

[![Version](https://img.shields.io/badge/version-1.1.0-blue.svg)](CHANGELOG.md)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Cost](https://img.shields.io/badge/cost-%240-brightgreen.svg)](https://openrouter.ai)
[![OpenRouter](https://img.shields.io/badge/powered%20by-OpenRouter-purple.svg)](https://openrouter.ai)

by [Musab Kara](https://linkedin.com/in/musab-kara-85580612a) · [GitHub](https://github.com/SkyWalker2506)

Automated GitHub PR review via OpenRouter free models (Gemma 3 27B + Mistral 7B fallback). Posts AI-generated review comments on pull requests. $0 cost.

## Install

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/SkyWalker2506/claude-marketplace/main/install.sh) ai-review
```

Or via Claude Code native marketplace:

```bash
claude plugin install ai-review@musabkara-claude-marketplace
```

## Commands

| Command | Description |
|---------|-------------|
| `/ai-review` | Review current branch PR |
| `/ai-review <PR#>` | Review specific PR |

## Setup

Add to `~/.claude/secrets/secrets.env`:
```
OPENROUTER_API_KEY=<from openrouter.ai>
```

Then add the workflow from `.github/workflows/ai-review.yml` (included in this repo) to any repo you want auto-reviewed on every PR.

## Example Output

When a PR is opened, the bot posts a comment like this:

---

**🤖 AI Code Review**

## Summary
This PR adds rate limiting middleware to the Express API. It introduces the `express-rate-limit` package and applies a 100 req/min limit to all `/api/*` routes.

## Issues
- **Line 23**: The rate limit window is set to `60 * 1000` ms but the comment says "1 hour" — this is 1 minute. Update the comment or the value.
- **Line 41**: `store` is not configured; defaults to in-memory, which resets on server restart. For production, use Redis store.

## Suggestions
- Consider applying stricter limits to `/api/auth/*` endpoints (e.g. 10 req/min) to protect against brute force.
- Add a `X-RateLimit-*` header response so clients know their quota.

---
_Powered by [ccplugin-ai-review](https://github.com/SkyWalker2506/ccplugin-ai-review) · Free via OpenRouter_

---

## Models

| Role | Model | Notes |
|------|-------|-------|
| Primary | `google/gemma-3-27b-it:free` | Best quality |
| Fallback | `mistralai/mistral-7b-instruct:free` | Used if primary at capacity |

## Part of

- [claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code
- [Plugin Marketplace](https://github.com/SkyWalker2506/claude-marketplace) — Browse & install all plugins
- [ClaudeHQ](https://github.com/SkyWalker2506/ClaudeHQ) — Claude ecosystem HQ

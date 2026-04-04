# ai-review — Claude Code Plugin

Automated GitHub PR review via OpenRouter free models (Gemma 3 27B). Posts AI-generated review comments on pull requests. $0 cost.

## Install

```bash
claude plugin install ai-review@SkyWalker2506-claude-plugins
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

## Part of

[SkyWalker2506/claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code

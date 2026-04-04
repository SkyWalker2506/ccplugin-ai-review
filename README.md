# ai-review — Claude Code Plugin

by [Musab Kara](https://linkedin.com/in/musab-kara-85580612a) · [GitHub](https://github.com/SkyWalker2506)

Automated GitHub PR review via OpenRouter free models (Gemma 3 27B). Posts AI-generated review comments on pull requests. $0 cost.

## Install

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

## Part of

- [claude-config](https://github.com/SkyWalker2506/claude-config) — Multi-Agent OS for Claude Code (110 agents, local-first routing)
- [Plugin Marketplace](https://github.com/SkyWalker2506/claude-marketplace) — Browse & install all 14 plugins

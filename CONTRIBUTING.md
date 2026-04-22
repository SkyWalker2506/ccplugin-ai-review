# Contributing to ccplugin-ai-review

Thank you for your interest in contributing!

## This Plugin is Markdown-Only

There is no build step, no compiled code, and no test runner. All plugin logic lives in:

- `commands/ai-review.md` — Claude Code command definition
- `skills/ai-review/SKILL.md` — Skill trigger and setup docs
- `.github/workflows/ai-review.yml` — GitHub Actions workflow
- `.claude-plugin/plugin.json` — Plugin metadata

## How to Contribute

### 1. Fork and clone

```bash
git clone https://github.com/<your-username>/ccplugin-ai-review.git
cd ccplugin-ai-review
```

### 2. Create a branch

Use a descriptive branch name:

```bash
git checkout -b fix/handle-empty-diff
git checkout -b feat/add-model-option
git checkout -b docs/improve-setup-guide
```

### 3. Make your changes

Edit the relevant markdown or YAML files. Keep changes focused and minimal.

### 4. Commit

Use conventional commits:

```
feat: add support for specifying model via argument
fix: handle empty diff gracefully
docs: clarify API key setup instructions
chore: update .gitignore
```

### 5. Open a Pull Request

Push your branch and open a PR against `main`. Describe:
- What problem you are solving
- What you changed
- How to verify the change works

## Guidelines

- Keep PRs small and focused
- Update CHANGELOG.md under `[Unreleased]` for user-visible changes
- Do not commit secrets or API keys
- YAML workflows must be valid (check with `python3 -c "import yaml; yaml.safe_load(open('file.yml'))"`)

## Reporting Issues

Open a GitHub issue with:
- What you expected to happen
- What actually happened
- Steps to reproduce

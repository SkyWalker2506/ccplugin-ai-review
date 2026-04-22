# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-04-22

### Added
- `.github/workflows/ai-review.yml` — GitHub Actions workflow now ships with the plugin (previously only documented)
- Model fallback: if `google/gemma-3-27b-it:free` is at capacity, automatically retries with `mistralai/mistral-7b-instruct:free`
- `.gitignore` for OS/IDE artifacts and forge directories
- `CHANGELOG.md` (this file)
- `CONTRIBUTING.md` with fork/PR guide
- README badges (version, license, free tier)
- Example review output section in README

### Changed
- `commands/ai-review.md` — Added input validation (PR number, API key check, empty diff handling), structured error messages, dual model support
- `skills/ai-review/SKILL.md` — Updated GHA snippet with fallback loop, structured review prompt, PR number/title context
- `.claude-plugin/plugin.json` — Added `tags`, `min_claude_version`, `commands`, `skills` arrays for marketplace discovery
- Bumped version to 1.1.0

### Fixed
- Empty diff case no longer crashes silently
- Missing API key now shows actionable setup instructions
- Non-numeric PR number argument no longer passes invalid value to `gh pr view`
- Large diffs truncated at 8000 chars to prevent OpenRouter token overflow

## [1.0.0] - 2026-04-01

### Added
- Initial release
- `/ai-review` command for Claude Code
- `ai-review` skill with GitHub Actions integration documentation
- OpenRouter free model integration (Gemma 3 27B)
- $0 cost PR review via free tier

# claude-code-codex-review

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that runs code reviews using the [OpenAI Codex CLI](https://github.com/openai/codex), giving you a second opinion from a different AI model.

## What it does

When invoked, this skill calls `codex review` on your code changes and presents the findings. It supports:

1. **Uncommitted changes** - review staged, unstaged, and untracked files (default)
2. **Branch diffs** - review all changes against a base branch
3. **Specific commits** - review changes introduced by a single commit
4. **Custom instructions** - pass additional review focus areas

## Prerequisites

- **Codex CLI** - install with `npm install -g @openai/codex`
- **OpenAI authentication** - run `codex login` to authenticate
- **Git repository** - must be run from inside a git repo

## Installation

### As a personal skill (available in all projects)

```bash
# Clone to a temporary location
git clone https://github.com/bszymi/claude-code-codex-review /tmp/claude-code-codex-review

# Copy the skill to your personal skills directory
cp -r /tmp/claude-code-codex-review/skills/codex-review ~/.claude/skills/
```

### As a project skill (available in one project)

```bash
# From your project root
cp -r /path/to/claude-code-codex-review/skills/codex-review .claude/skills/
```

## Usage

```
/codex-review                              # Review uncommitted changes
/codex-review --base main                  # Review current branch vs main
/codex-review --commit abc123              # Review a specific commit
/codex-review "focus on security"          # Uncommitted changes with custom instructions
/codex-review --base develop "check perf"  # Branch diff with custom instructions
```

## License

MIT

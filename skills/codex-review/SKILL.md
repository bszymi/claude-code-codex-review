---
name: codex-review
description: Run a code review using OpenAI Codex CLI and return its findings. Use when the user wants an external second opinion on their code changes.
user-invocable: true
argument-hint: [--base <branch> | --commit <sha> | --uncommitted | "custom instructions"]
---

This skill runs a code review using the OpenAI Codex CLI (`codex review`) and presents the results.

## Prerequisites

Requires the Codex CLI to be installed and authenticated:
- `codex` binary in PATH
- Valid OpenAI authentication (run `codex login` if needed)

If `codex` is not found, stop and tell the user:
```
Codex CLI is not installed. Install it with:
  npm install -g @openai/codex
Then authenticate:
  codex login
```

## Step 1: Validate Environment

Check that codex is available:
```bash
which codex
```

Check that the current directory is a git repository:
```bash
git rev-parse --is-inside-work-tree
```

If either check fails, stop and inform the user.

## Step 2: Determine Review Scope

Parse the argument to decide which `codex review` flags to use:

- **No argument or `--uncommitted`**: Review all uncommitted changes (staged, unstaged, untracked). Use `codex review --uncommitted`.
- **`--base <branch>`**: Review changes against a base branch. Use `codex review --base <branch>`.
- **`--commit <sha>`**: Review a specific commit. Use `codex review --commit <sha>`.
- **Any other text**: Treat as custom review instructions passed as the prompt. Combine with `--uncommitted` as the default scope.

If `--uncommitted` is used (or defaulted to), first verify there are actual changes:
```bash
git status --porcelain
```
If there are no changes, tell the user there's nothing to review.

## Step 3: Run Codex Review

Execute the codex review command. Use a timeout of 300 seconds (5 minutes) since reviews can take time on large diffs.

Examples of the command:

```bash
# Default: review uncommitted changes
codex review --uncommitted

# Review against a base branch
codex review --base main

# Review a specific commit
codex review --commit abc123

# With custom instructions
codex review --uncommitted "Focus on security vulnerabilities and error handling"

# Base branch with custom instructions
codex review --base main "Check for performance issues"
```

Capture both stdout and stderr. The command runs non-interactively and prints the review to stdout.

## Step 4: Present Results

Present the Codex review output to the user under a clear heading:

```
## Codex Review

**Scope:** {what was reviewed - uncommitted changes / branch diff / commit}

{codex review output}
```

If Codex found no issues, say so clearly.

## Error Handling

- **codex not found**: Tell user to install with `npm install -g @openai/codex`
- **Not a git repo**: Tell user to run from inside a git repository
- **Auth failure**: Tell user to run `codex login`
- **No changes to review**: Tell user there are no uncommitted changes
- **Timeout**: Inform user the review timed out, suggest reviewing a smaller scope (e.g. a specific commit instead of a full branch diff)
- **Non-zero exit code**: Show the stderr output to the user

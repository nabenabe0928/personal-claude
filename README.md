# Claude Code: Good First-Start Setups

## CLI Quick Reference

```bash
claude                        # Start interactive session
claude -c                     # Continue last session
claude -r                     # Resume a specific session
```

## First Things to Do

### 1. Generate a CLAUDE.md

`CLAUDE.md` is a project instruction file that Claude reads at the start of every session. It tells Claude how to build, test, and work in your repo.

```
claude
/init
```

This analyzes your codebase and generates a starter `CLAUDE.md` with build commands, architecture notes, and coding conventions.

#### CLAUDE.md locations

| File | Scope | Git-tracked |
|------|-------|-------------|
| `./CLAUDE.md` or `./.claude/CLAUDE.md` | Project (shared) | Yes |
| `./CLAUDE.local.md` | Project (personal) | No |
| `~/.claude/CLAUDE.md` | All projects | No |

### 2. Configure Permissions

Reduce permission prompts by allowlisting safe commands. Edit `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(npm test *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(git commit *)"
    ],
    "deny": [
      "Bash(rm -rf *)"
    ]
  }
}
```

Or use the interactive command:

```
/permissions
```

> [!TIPS]
> Use `/fewer-permission-prompts` to auto-generate an allowlist from your session history.

#### Permission modes

Switch modes with **Shift+Tab** during a session:

| Mode | Behavior |
|------|----------|
| `default` | Prompts for each tool use |
| `plan` | Claude explores and plans without editing |
| `acceptEdits` | Auto-approves file edits in the working directory |
| `auto` | Auto-approves routine work with safety checks |

### 3. Set Up MCP Servers (Optional)

MCP (Model Context Protocol) connects Claude to external tools like GitHub, Jira, databases, and more.

```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_TOKEN"
```

Manage servers with `/mcp` in a session.

### 4. Add Path-Scoped Rules (Optional)

Create rules that apply only to specific files. Place them in `.claude/rules/`:

`.claude/rules/api-design.md`:

```markdown
---
paths:
  - "src/api/**/*.ts"
---
# API Rules
- All endpoints require input validation
- Use standard error format
```

## Essential Slash Commands

| Command | Purpose |
|---------|---------|
| `/init` | Generate a CLAUDE.md from your codebase |
| `/plan` | Switch to plan mode (explore without editing) |
| `/permissions` | Review and manage tool permissions |
| `/memory` | View/edit CLAUDE.md and auto memory |
| `/compact` | Summarize conversation to save context |
| `/clear` | Clear conversation history |
| `/model` | Switch AI model |
| `/help` | Show all available commands |

## Key Shortcuts

| Shortcut | Action |
|----------|--------|
| `Enter` | Submit message |
| `Ctrl+J` | Insert newline without submitting |
| `Shift+Tab` | Cycle permission modes |
| `Ctrl+C` | Cancel current operation |
| `Ctrl+L` | Clear screen |
| `Up/Down` | Navigate message history |
| `Ctrl+R` | Search history |

Customize shortcuts in `~/.claude/keybindings.json`.

## Recommended Workflow

### Explore → Plan → Code → Commit

```
# 1. Explore: understand the codebase
/plan
explain the authentication flow in src/auth/

# 2. Plan: align on approach
I want to add Google OAuth. What files need to change?

# 3. Implement: switch to default mode and code
(Shift+Tab to switch mode)
Implement the OAuth flow from your plan. Write tests, run them, fix failures.

# 4. Commit
commit my changes and create a pull request
```

## Settings Files Reference

| File | Purpose |
|------|---------|
| `~/.claude/settings.json` | User settings (all projects) |
| `.claude/settings.json` | Project settings (shared via git) |
| `.claude/settings.local.json` | Local project overrides (not tracked) |
| `~/.claude/keybindings.json` | Keyboard shortcuts |

## Hooks (Automation)

Hooks are shell commands that run at lifecycle events. Configure in `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/validate.sh"
          }
        ]
      }
    ]
  }
}
```

Available events: `SessionStart`, `SessionEnd`, `PreToolUse`, `PostToolUse`.

## Custom Subagents (Optional)

Define specialized agents in `.claude/agents/`:

`.claude/agents/reviewer.md`:

```markdown
---
name: reviewer
description: Reviews code for quality issues
---
Review the code for correctness, performance, and maintainability.
Flag any security concerns.
```

Invoke with: `"Use the reviewer agent on this PR"`

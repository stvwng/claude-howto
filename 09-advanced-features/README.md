<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# Advanced Features

Comprehensive guide to Claude Code's advanced capabilities including planning mode, extended thinking, background tasks, permission modes, print mode (non-interactive), session management, interactive features, remote control, web sessions, desktop app, task list, prompt suggestions, git worktrees, sandboxing, managed settings, and configuration.

## Table of Contents

1. [Overview](#overview)
2. [Planning Mode](#planning-mode)
3. [Extended Thinking](#extended-thinking)
4. [Background Tasks](#background-tasks)
5. [Permission Mode](#permission-mode)
6. [Headless Mode](#headless-mode)
7. [Session Management](#session-management)
8. [Interactive Features](#interactive-features)
9. [Remote Control](#remote-control)
10. [Web Sessions](#web-sessions)
11. [Desktop App](#desktop-app)
12. [Task List](#task-list)
13. [Prompt Suggestions](#prompt-suggestions)
14. [Git Worktrees](#git-worktrees)
15. [Sandboxing](#sandboxing)
16. [Managed Settings (Enterprise)](#managed-settings-enterprise)
17. [Configuration and Settings](#configuration-and-settings)
18. [Best Practices](#best-practices)
19. [Related Concepts](#related-concepts)

---

## Overview

Advanced features in Claude Code extend the core capabilities with planning, reasoning, automation, and control mechanisms. These features enable sophisticated workflows for complex development tasks, code review, automation, and multi-session management.

**Key advanced features include:**
- **Planning Mode**: Create detailed implementation plans before coding
- **Extended Thinking**: Deep reasoning for complex problems
- **Background Tasks**: Run long operations without blocking the conversation
- **Permission Modes**: Control what Claude can do (`default`, `acceptEdits`, `plan`, `dontAsk`, `bypassPermissions`)
- **Print Mode**: Run Claude Code non-interactively for automation and CI/CD (`claude -p`)
- **Session Management**: Manage multiple work sessions
- **Interactive Features**: Keyboard shortcuts, multi-line input, and command history
- **Remote Control**: Control Claude Code from Claude.ai or the Claude app
- **Web Sessions**: Run Claude Code in the browser at claude.ai/code
- **Desktop App**: Standalone app for visual diff review and multiple sessions
- **Task List**: Persistent task tracking across context compactions
- **Prompt Suggestions**: Smart command suggestions based on context
- **Git Worktrees**: Isolated worktree branches for parallel work
- **Sandboxing**: OS-level filesystem and network isolation
- **Managed Settings**: Enterprise deployment via plist, Registry, or managed files
- **Configuration**: Customize behavior with JSON configuration files

---

## Planning Mode

Planning mode allows Claude to think through complex tasks before implementing them, creating a detailed plan that you can review and approve.

### What is Planning Mode?

Planning mode is a two-phase approach:
1. **Planning Phase**: Claude analyzes the task and creates a detailed implementation plan
2. **Implementation Phase**: After approval, Claude executes the plan

### When to Use Planning Mode

✅ Use planning mode for:
- Complex multi-file refactoring
- New feature implementations
- Architectural changes
- Database migrations
- Major API redesigns

❌ Don't use planning mode for:
- Simple bug fixes
- Formatting changes
- Single-file edits
- Quick queries

### Activating Planning Mode

**Slash command**:
```bash
/plan Implement user authentication system
```

**CLI flag**:
```bash
claude --permission-mode plan
```

**Set as default**:
```json
{
  "permissions": {
    "defaultMode": "plan"
  }
}
```

**Keyboard shortcut**:
- `Shift + Tab` - Toggle permission modes (including plan)
- `Alt + M` - Toggle permission modes (Windows/Linux)

### Benefits of Planning Mode

- **Structured implementation approach**: Detailed breakdown of implementation steps
- **Review and approval**: Approve or adjust the plan before execution
- **Risk identification**: Identify potential issues before implementation
- **Clear phases**: Organized implementation phases and milestones

### Example: Feature Implementation

```
User: /plan Add a real-time notification system

Claude: I'll create a plan for implementing real-time notifications.

## Implementation Plan

### Phase 1: Infrastructure Setup
1. Install WebSocket libraries (socket.io)
2. Set up WebSocket server
3. Configure connection management

### Phase 2: Backend Implementation
4. Create notification service
5. Implement notification queue
6. Add database schema for notifications
7. Create API endpoints

### Phase 3: Frontend Integration
8. Set up WebSocket client
9. Create notification UI components
10. Implement notification state management
11. Add notification permissions

### Phase 4: Testing & Deployment
12. Write unit tests
13. Write integration tests
14. Update documentation
15. Deploy to staging

Estimated time: 3-4 hours
Files to modify: 12
New files to create: 8

Ready to proceed? (yes/no/modify plan)

User: yes

Claude: [Begins implementation following the plan]
```

### Plan Modifications

```
User: Modify the plan - skip the queue for now, we'll add it later

Claude: Updated plan:
[Shows modified plan with queue removed]

User: Looks good, proceed

Claude: [Implements modified plan]
```

### Planning Mode Configuration

Planning mode is activated via the CLI flag or slash command:

```bash
# Activate plan mode via CLI
claude --permission-mode plan

# Or use the /plan slash command inside the REPL
/plan Implement user authentication system
```

**Model alias for planning**: Use `opusplan` as a model alias to use Opus for planning and Sonnet for execution:

```bash
claude --model opusplan "design and implement the new API"
```

**Edit plan externally**: Press `Ctrl+G` to open the current plan in your external editor for detailed modifications.

---

## Extended Thinking

Extended thinking allows Claude to spend more time reasoning about complex problems before providing a solution.

### What is Extended Thinking?

Extended thinking is a deliberate, step-by-step reasoning process where Claude:
- Breaks down complex problems
- Considers multiple approaches
- Evaluates trade-offs
- Reasons through edge cases

### Activating Extended Thinking

**Keyboard shortcut**:
- `Option + T` (macOS) / `Alt + T` (Windows/Linux) - Toggle extended thinking

**Automatic activation**:
- Enabled by default for all models (Opus 4.6, Sonnet 4.6, Haiku 4.5)
- Opus 4.6: Adaptive reasoning with effort levels (low/medium/high)
- Other models: Fixed budget up to 31,999 tokens

**Configuration methods**:
- Toggle: `Alt+T` / `Option+T`, or via `/config`
- View reasoning: `Ctrl+O` (verbose mode)

**Custom budget**:
```bash
export MAX_THINKING_TOKENS=1024
```

**Effort level** (Opus 4.6 only):
```bash
export CLAUDE_CODE_EFFORT_LEVEL=high   # low, medium, or high
```

> **Note:** Words like "think" or "ultrathink" in prompts are interpreted as regular prompt instructions, not special keywords or triggers.

### Benefits of Extended Thinking

- **Deep reasoning**: Spend more time thinking about complex problems
- **Better architectural decisions**: Make informed choices with comprehensive evaluation
- **Consideration of edge cases**: Think through potential issues and scenarios
- **Thorough analysis**: Structured approach to complex problem-solving

### Example: Architectural Decision

```
User: /think Should we use microservices or a monolith for our e-commerce platform?

Claude: Let me think through this architectural decision carefully...

<extended_thinking>
## Context Analysis
- Team size: 5 developers
- Expected traffic: 10k daily users initially
- Need to scale to 100k+ users
- Limited DevOps resources
- 6-month timeline to MVP

## Approach 1: Microservices
Pros:
+ Independent scaling
+ Technology flexibility
+ Team independence
+ Fault isolation

Cons:
- Operational complexity
- Network overhead
- Distributed tracing needed
- Requires DevOps expertise
- Slower initial development

## Approach 2: Monolith
Pros:
+ Simpler deployment
+ Faster initial development
+ Easier debugging
+ Lower operational overhead
+ Single codebase

Cons:
- Scaling challenges later
- All-or-nothing deployments
- Technology lock-in
- Potential for tight coupling

## Decision Factors
1. Team size (5 devs) → Favors monolith
2. DevOps resources (limited) → Favors monolith
3. Timeline (6 months) → Favors monolith
4. Future scale (100k users) → Achievable with monolith + caching
5. Complexity budget → Limited

## Recommendation
Start with a **modular monolith**:
- Single deployment unit
- Clear module boundaries
- Clean interfaces between modules
- Prepare for future extraction to microservices if needed

This gives you:
✓ Fast initial development
✓ Low operational complexity
✓ Option to extract services later
✓ Clear architecture

Migration path: When you reach 50k+ users or specific modules need independent scaling, extract those modules into microservices.
</extended_thinking>

**Recommendation**: Implement a modular monolith with clean module boundaries.

This approach balances your current constraints (team size, timeline, DevOps resources) with future scalability needs. You can migrate specific modules to microservices later when there's a clear business need.
```

### Extended Thinking Configuration

Extended thinking is controlled via environment variables and keyboard shortcuts rather than JSON config:

```bash
# Set thinking token budget
export MAX_THINKING_TOKENS=16000

# Set effort level (Opus 4.6 only)
export CLAUDE_CODE_EFFORT_LEVEL=high
```

Toggle during a session with `Alt+T` / `Option+T`, or configure via `/config`.

---

## Background Tasks

Background tasks allow long-running operations to execute without blocking your conversation.

### What Are Background Tasks?

Background tasks run asynchronously while you continue working:
- Long test suites
- Build processes
- Database migrations
- Deployment scripts
- Analysis tools

**Basic Usage:**
```bash
User: Run tests in background

Claude: Started task bg-1234

/task list           # Show all tasks
/task status bg-1234 # Check progress
/task show bg-1234   # View output
/task cancel bg-1234 # Cancel task
```

### Starting Background Tasks

```
User: Run the full test suite in the background

Claude: Starting tests in background (task-id: bg-1234)
You can continue working while tests run.

[You can continue the conversation]

User: Meanwhile, let's refactor the auth module

Claude: [Works on auth module while tests run in background]

[Later, when tests complete]

Claude: 📢 Background task bg-1234 completed:
✅ 245 tests passed
❌ 3 tests failed
View results: /task show bg-1234
```

### Managing Background Tasks

**List active tasks**:
```
User: /task list

Active background tasks:
1. [bg-1234] Running tests (50% complete, 2min remaining)
2. [bg-1235] Building Docker image (25% complete, 8min remaining)
3. [bg-1236] Deploying to staging (90% complete, 30sec remaining)
```

**Check task status**:
```
User: /task status bg-1234

Task bg-1234: Running tests
Status: In progress
Progress: 120/245 tests (49%)
Started: 2025-11-08 10:30:15
Estimated completion: 2025-11-08 10:34:22
```

**View task output**:
```
User: /task show bg-1234

[Shows live output from the test run]
```

**Cancel task**:
```
User: /task cancel bg-1234

Cancelled background task bg-1234
```

### Example: Parallel Development

```
User: Run the build in the background

Claude: Starting build... (task-id: bg-5001)

User: Also run the linter in background

Claude: Starting linter... (task-id: bg-5002)

User: While those run, let's implement the new API endpoint

Claude: [Implements API endpoint while build and linter run]

[10 minutes later]

Claude: 📢 Build completed successfully (bg-5001)
📢 Linter found 12 issues (bg-5002)

User: Show me the linter issues

Claude: [Shows linter output from bg-5002]
```

### Configuration

```json
{
  "backgroundTasks": {
    "enabled": true,
    "maxConcurrentTasks": 5,
    "notifyOnCompletion": true,
    "autoCleanup": true,
    "logOutput": true
  }
}
```

---

## Permission Modes

Permission modes control what actions Claude can take without explicit approval.

### Available Permission Modes

| Mode | Behavior |
|---|---|
| `default` | Standard: prompts for permission |
| `acceptEdits` | Auto-accepts file edits |
| `plan` | Read-only analysis mode |
| `dontAsk` | Auto-denies unless pre-approved |
| `bypassPermissions` | Skips all checks (dangerous) |

### Activation Methods

**Keyboard shortcut**:
```bash
Shift + Tab  # or Alt + M on Windows/Linux
```

**Slash command**:
```bash
/plan                  # Enter plan mode
```

**CLI flag**:
```bash
claude --permission-mode plan
```

**Environment variable**:
```bash
export CLAUDE_PERMISSION_MODE=plan
```

### Permission Mode Examples

#### Default Mode
Claude asks for confirmation on significant actions:

```
User: Fix the bug in auth.ts

Claude: I need to modify src/auth.ts to fix the bug.
The change will update the password validation logic.

Approve this change? (yes/no/show)
```

#### Plan Mode
Review implementation plan before execution:

```
User: /plan Implement user authentication system

Claude: I'll create a plan for implementing authentication.

## Implementation Plan
[Detailed plan with phases and steps]

Ready to proceed? (yes/no/modify)
```

#### Accept Edits Mode
Automatically accept file modifications:

```
User: acceptEdits
User: Fix the bug in auth.ts

Claude: [Makes changes without asking]
```

### Use Cases

**Code Review**:
```
User: claude --permission-mode plan
User: Review this PR and suggest improvements

Claude: [Reads code, provides feedback, but cannot modify]
```

**Pair Programming**:
```
User: claude --permission-mode default
User: Let's implement the feature together

Claude: [Asks for approval before each change]
```

**Automated Tasks**:
```
User: claude --permission-mode acceptEdits
User: Fix all linting issues in the codebase

Claude: [Auto-accepts file edits without asking]
```

---

## Headless Mode

Print mode (`claude -p`) allows Claude Code to run without interactive input, perfect for automation and CI/CD. This is the non-interactive mode, replacing the older `--headless` flag.

### What is Print Mode?

Print mode enables:
- Automated script execution
- CI/CD integration
- Batch processing
- Scheduled tasks

### Running in Print Mode (Non-Interactive)

```bash
# Run specific task
claude -p "Run all tests"

# Process piped content
cat error.log | claude -p "Analyze these errors"

# CI/CD integration (GitHub Actions)
- name: AI Code Review
  run: claude -p "Review PR"
```

### Additional Print Mode Usage Examples

```bash
# Run a specific task with output capture
claude -p "Run all tests and generate coverage report"

# With structured output
claude -p --output-format json "Analyze code quality"

# With input from stdin
echo "Analyze code quality" | claude -p "explain this"
```

### Example: CI/CD Integration

**GitHub Actions**:
```yaml
# .github/workflows/code-review.yml
name: AI Code Review

on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run Claude Code Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p --output-format json \
            --max-turns 3 \
            "Review this PR for:
            - Code quality issues
            - Security vulnerabilities
            - Performance concerns
            - Test coverage
            Output results as JSON" > review.json

      - name: Post Review Comment
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const review = JSON.parse(fs.readFileSync('review.json', 'utf8'));
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: JSON.stringify(review, null, 2)
            });
```

### Print Mode Configuration

Print mode (`claude -p`) supports several flags for automation:

```bash
# Limit autonomous turns
claude -p --max-turns 5 "refactor this module"

# Structured JSON output
claude -p --output-format json "analyze this codebase"

# With schema validation
claude -p --json-schema '{"type":"object","properties":{"issues":{"type":"array"}}}' \
  "find bugs in this code"

# Disable session persistence
claude -p --no-session-persistence "one-off analysis"
```

---

## Session Management

Manage multiple Claude Code sessions effectively.

### Session Management Commands

| Command | Description |
|---------|-------------|
| `/resume` | Resume a conversation by ID or name |
| `/rename` | Name the current session |
| `/fork` | Fork current session into a new branch |
| `claude -c` | Continue most recent conversation |
| `claude -r "session"` | Resume session by name or ID |

### Resuming Sessions

**Continue last conversation**:
```bash
claude -c
```

**Resume a named session**:
```bash
claude -r "auth-refactor" "finish this PR"
```

**Rename the current session** (inside the REPL):
```
/rename auth-refactor
```

### Forking Sessions

Fork a session to try an alternative approach without losing the original:

```
/fork
```

Or from the CLI:
```bash
claude --resume auth-refactor --fork-session "try OAuth instead"
```

### Session Persistence

Sessions are automatically saved and can be resumed:

```bash
# Continue last conversation
claude -c

# Resume specific session by name or ID
claude -r "auth-refactor"

# Resume and fork for experimentation
claude --resume auth-refactor --fork-session "alternative approach"
```

---

## Interactive Features

### Keyboard Shortcuts

Claude Code supports keyboard shortcuts for efficiency. Here's the complete reference from official docs:

| Shortcut | Description |
|----------|-------------|
| `Ctrl+C` | Cancel current input/generation |
| `Ctrl+D` | Exit Claude Code |
| `Ctrl+G` | Edit plan in external editor |
| `Ctrl+L` | Clear terminal screen |
| `Ctrl+O` | Toggle verbose output (view reasoning) |
| `Ctrl+R` | Reverse search history |
| `Ctrl+T` | Toggle task list view |
| `Ctrl+B` | Background running tasks |
| `Esc+Esc` | Rewind code/conversation |
| `Shift+Tab` / `Alt+M` | Toggle permission modes |
| `Option+P` / `Alt+P` | Switch model |
| `Option+T` / `Alt+T` | Toggle extended thinking |

**Line Editing (standard readline shortcuts):**

| Shortcut | Action |
|----------|--------|
| `Ctrl + A` | Move to line start |
| `Ctrl + E` | Move to line end |
| `Ctrl + K` | Cut to end of line |
| `Ctrl + U` | Cut to start of line |
| `Ctrl + W` | Delete word backward |
| `Ctrl + Y` | Paste (yank) |
| `Tab` | Autocomplete |
| `↑ / ↓` | Command history |

### Tab Completion

Claude Code provides intelligent tab completion:

```
User: /rew<TAB>
→ /rewind

User: /plu<TAB>
→ /plugin

User: /plugin <TAB>
→ /plugin install
→ /plugin enable
→ /plugin disable
```

### Command History

Access previous commands:

```
User: <↑>  # Previous command
User: <↓>  # Next command
User: Ctrl+R  # Search history

(reverse-i-search)`test': run all tests
```

### Multi-line Input

For complex queries, use multi-line mode:

```bash
User: \
> Long complex prompt
> spanning multiple lines
> \end
```

**Example:**

```
User: \
> Implement a user authentication system
> with the following requirements:
> - JWT tokens
> - Email verification
> - Password reset
> - 2FA support
> \end

Claude: [Processes the multi-line request]
```

### Inline Editing

Edit commands before sending:

```
User: Deploy to prodcution<Backspace><Backspace>uction

[Edit in-place before sending]
```

### Vim Mode

Enable Vi/Vim keybindings for text editing:

**Activation**:
- Use `/vim` command or `/config` to enable
- Mode switching with `Esc` for NORMAL, `i/a/o` for INSERT

**Navigation keys**:
- `h` / `l` - Move left/right
- `j` / `k` - Move down/up
- `w` / `b` / `e` - Move by word
- `0` / `$` - Move to line start/end
- `gg` / `G` - Jump to start/end of text

**Text objects**:
- `iw` / `aw` - Inner/around word
- `i"` / `a"` - Inner/around quoted string
- `i(` / `a(` - Inner/around parentheses

### Bash Mode

Execute shell commands directly with `!` prefix:

```bash
! npm test
! git status
! cat src/index.js
```

Use this for quick command execution without switching contexts.

---

## Remote Control

Remote Control allows you to control a locally running Claude Code instance from Claude.ai or the Claude app. This is useful when you want to interact with Claude Code through a web interface while it runs against your local codebase.

### Starting Remote Control

```bash
claude remote-control
```

This starts a Remote Control session that bridges your local Claude Code environment with the Claude.ai web interface. You can then issue commands from the browser that execute against your local file system and tools.

### Use Cases

- Control Claude Code from a mobile device or tablet
- Collaborate with team members through a shared web interface
- Use the richer Claude.ai UI while maintaining local tool execution

---

## Web Sessions

Web Sessions allow you to run Claude Code directly in the browser at claude.ai/code, or create web sessions from the CLI.

### Creating a Web Session

```bash
# Create a new web session from the CLI
claude --remote "implement the new API endpoints"
```

This starts a Claude Code session on claude.ai that you can access from any browser.

### Resuming Web Sessions Locally

If you started a session on the web and want to continue it locally:

```bash
# Resume a web session in the local terminal
claude --teleport
```

Or from within an interactive REPL:
```
/teleport
```

### Use Cases

- Start work on one machine and continue on another
- Share a session URL with team members
- Use the web UI for visual diff review, then switch to terminal for execution

---

## Desktop App

The Claude Code Desktop App provides a standalone application for visual diff review and managing multiple sessions. Available for macOS and Windows.

### Handing Off from CLI

If you are in a CLI session and want to switch to the Desktop App:

```
/desktop
```

This transfers your current session to the Desktop App for a richer visual experience.

### Features

- Visual diff review for file changes
- Multiple simultaneous sessions in tabs
- Rich rendering of code, markdown, and diagrams
- Available for macOS and Windows

---

## Task List

The Task List feature provides persistent task tracking that survives context compactions (when the conversation history is trimmed to fit the context window).

### Toggling the Task List

Press `Ctrl+T` to toggle the task list view on or off during a session.

### Persistent Tasks

Tasks persist across context compactions, ensuring that long-running work items are not lost when the conversation context is trimmed. This is particularly useful for complex, multi-step implementations.

### Named Task Directories

Use the `CLAUDE_CODE_TASK_LIST_ID` environment variable to create named task directories shared across sessions:

```bash
export CLAUDE_CODE_TASK_LIST_ID=my-project-sprint-3
```

This allows multiple sessions to share the same task list, making it useful for team workflows or multi-session projects.

---

## Prompt Suggestions

Prompt Suggestions display grayed-out example commands based on your git history and current conversation context.

### How It Works

- Suggestions appear as grayed-out text below your input prompt
- Press `Tab` to accept the suggestion
- Press `Enter` to accept and immediately submit
- Suggestions are context-aware, drawing from git history and conversation state

### Disabling Prompt Suggestions

```bash
export CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION=false
```

---

## Git Worktrees

Git Worktrees allow you to start Claude Code in an isolated worktree, enabling parallel work on different branches without stashing or switching.

### Starting in a Worktree

```bash
# Start Claude Code in an isolated worktree
claude --worktree
# or
claude -w
```

### Worktree Location

Worktrees are created at:
```
<repo>/.claude/worktrees/<name>
```

### Auto-Cleanup

If no changes are made in the worktree, it is automatically cleaned up when the session ends.

### Use Cases

- Work on a feature branch while keeping main branch untouched
- Run tests in isolation without affecting the working directory
- Try experimental changes in a disposable environment

---

## Sandboxing

Sandboxing provides OS-level filesystem and network isolation for Bash commands executed by Claude Code. This is complementary to permission rules and provides an additional security layer.

### How It Works

- Bash commands run in a sandboxed environment with restricted filesystem access
- Network access can be isolated to prevent unintended external connections
- Works alongside permission rules for defense in depth

### Use Cases

- Running untrusted or generated code safely
- Preventing accidental modifications to files outside the project
- Restricting network access during automated tasks

---

## Managed Settings (Enterprise)

Managed Settings enable enterprise administrators to deploy Claude Code configuration across an organization using platform-native management tools.

### Deployment Methods

| Platform | Method |
|----------|--------|
| macOS | Managed plist files (MDM) |
| Windows | Windows Registry |
| Cross-platform | Managed configuration files |

### Available Managed Settings

| Setting | Description |
|---------|-------------|
| `disableBypassPermissionsMode` | Prevent users from enabling bypass permissions |
| `availableModels` | Restrict which models users can select |
| Custom policies | Organization-specific permission and tool policies |

### Example: macOS Plist

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>disableBypassPermissionsMode</key>
  <true/>
  <key>availableModels</key>
  <array>
    <string>claude-sonnet-4-6</string>
    <string>claude-haiku-4-5</string>
  </array>
</dict>
</plist>
```

---

## Configuration and Settings

### Configuration File Locations

1. **Global config**: `~/.claude/config.json`
2. **Project config**: `./.claude/config.json`
3. **User config**: `~/.config/claude-code/settings.json`

### Complete Configuration Example

**Core advanced features configuration:**

```json
{
  "permissions": {
    "mode": "default"
  },
  "hooks": {
    "PreToolUse:Edit": "eslint --fix ${file_path}",
    "PostToolUse:Write": "~/.claude/hooks/security-scan.sh"
  },
  "mcp": {
    "enabled": true,
    "servers": {
      "github": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-github"]
      }
    }
  }
}
```

**Extended configuration example:**

```json
{
  "permissions": {
    "mode": "default",
    "allowedTools": ["Bash(git log:*)", "Read"],
    "disallowedTools": ["Bash(rm -rf:*)"]
  },

  "hooks": {
    "PreToolUse": [{ "matcher": "Edit", "hooks": ["eslint --fix ${file_path}"] }],
    "PostToolUse": [{ "matcher": "Write", "hooks": ["~/.claude/hooks/security-scan.sh"] }],
    "Stop": [{ "hooks": ["~/.claude/hooks/notify.sh"] }]
  },

  "mcp": {
    "enabled": true,
    "servers": {
      "github": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-github"],
        "env": {
          "GITHUB_TOKEN": "${GITHUB_TOKEN}"
        }
      }
    }
  }
}
```

### Environment Variables

Override config with environment variables:

```bash
# Model selection
export ANTHROPIC_MODEL=claude-opus-4-6
export ANTHROPIC_DEFAULT_OPUS_MODEL=claude-opus-4-6
export ANTHROPIC_DEFAULT_SONNET_MODEL=claude-sonnet-4-6
export ANTHROPIC_DEFAULT_HAIKU_MODEL=claude-haiku-4-5

# API configuration
export ANTHROPIC_API_KEY=sk-ant-...

# Thinking configuration
export MAX_THINKING_TOKENS=16000
export CLAUDE_CODE_EFFORT_LEVEL=high

# Feature toggles
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=true
export CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=true
export CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION=false
export CLAUDE_CODE_ENABLE_TASKS=true

# MCP configuration
export MAX_MCP_OUTPUT_TOKENS=50000
export ENABLE_TOOL_SEARCH=true

# Task management
export CLAUDE_CODE_TASK_LIST_ID=my-project-tasks

# Agent teams (experimental)
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=true
```

### Configuration Management Commands

```
User: /config
[Opens interactive configuration menu]
```

The `/config` command provides an interactive menu to toggle settings such as:
- Extended thinking on/off
- Verbose output
- Permission mode
- Model selection

### Per-Project Configuration

Create `.claude/config.json` in your project:

```json
{
  "hooks": {
    "PreToolUse": [{ "matcher": "Bash", "hooks": ["npm test && npm run lint"] }]
  },
  "permissions": {
    "mode": "default"
  },
  "mcp": {
    "servers": {
      "project-db": {
        "command": "mcp-postgres",
        "env": {
          "DATABASE_URL": "${PROJECT_DB_URL}"
        }
      }
    }
  }
}
```

---

## Best Practices

### Planning Mode
- ✅ Use for complex multi-step tasks
- ✅ Review plans before approving
- ✅ Modify plans when needed
- ❌ Don't use for simple tasks

### Extended Thinking
- ✅ Use for architectural decisions
- ✅ Use for complex problem-solving
- ✅ Review the thinking process
- ❌ Don't use for simple queries

### Background Tasks
- ✅ Use for long-running operations
- ✅ Monitor task progress
- ✅ Handle task failures gracefully
- ❌ Don't start too many concurrent tasks

### Permissions
- ✅ Use `plan` for code review (read-only)
- ✅ Use `default` for interactive development
- ✅ Use `acceptEdits` for automation workflows
- ❌ Don't use `bypassPermissions` unless absolutely necessary

### Sessions
- ✅ Use separate sessions for different tasks
- ✅ Save important session states
- ✅ Clean up old sessions
- ❌ Don't mix unrelated work in one session

---

## Additional Resources

For more information about Claude Code and related features:

- [Official Interactive Mode Documentation](https://code.claude.com/docs/en/interactive-mode)
- [Official Headless Mode Documentation](https://code.claude.com/docs/en/headless)
- [CLI Reference](https://code.claude.com/docs/en/cli-reference)
- [Checkpoints Guide](../08-checkpoints/) - Session management and rewinding
- [Slash Commands](../01-slash-commands/) - Command reference
- [Memory Guide](../02-memory/) - Persistent context
- [Skills Guide](../03-skills/) - Autonomous capabilities
- [Subagents Guide](../04-subagents/) - Delegated task execution
- [MCP Guide](../05-mcp/) - External data access
- [Hooks Guide](../06-hooks/) - Event-driven automation
- [Plugins Guide](../07-plugins/) - Bundled extensions

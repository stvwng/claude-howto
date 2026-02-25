<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# MCP (Model Context Protocol)

This folder contains comprehensive documentation and examples for MCP server configurations and usage with Claude Code.

## Overview

MCP (Model Context Protocol) is a standardized way for Claude to access external tools, APIs, and real-time data sources. Unlike Memory, MCP provides live access to changing data.

Key characteristics:
- Real-time access to external services
- Live data synchronization
- Extensible architecture
- Secure authentication
- Tool-based interactions

## MCP Architecture

```mermaid
graph TB
    A["Claude"]
    B["MCP Server"]
    C["External Service"]

    A -->|Request: list_issues| B
    B -->|Query| C
    C -->|Data| B
    B -->|Response| A

    A -->|Request: create_issue| B
    B -->|Action| C
    C -->|Result| B
    B -->|Response| A

    style A fill:#e1f5fe,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
    style C fill:#e8f5e9,stroke:#333,color:#333
```

## MCP Ecosystem

```mermaid
graph TB
    A["Claude"] -->|MCP| B["Filesystem<br/>MCP Server"]
    A -->|MCP| C["GitHub<br/>MCP Server"]
    A -->|MCP| D["Database<br/>MCP Server"]
    A -->|MCP| E["Slack<br/>MCP Server"]
    A -->|MCP| F["Google Docs<br/>MCP Server"]

    B -->|File I/O| G["Local Files"]
    C -->|API| H["GitHub Repos"]
    D -->|Query| I["PostgreSQL/MySQL"]
    E -->|Messages| J["Slack Workspace"]
    F -->|Docs| K["Google Drive"]

    style A fill:#e1f5fe,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
    style C fill:#f3e5f5,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#f3e5f5,stroke:#333,color:#333
    style F fill:#f3e5f5,stroke:#333,color:#333
    style G fill:#e8f5e9,stroke:#333,color:#333
    style H fill:#e8f5e9,stroke:#333,color:#333
    style I fill:#e8f5e9,stroke:#333,color:#333
    style J fill:#e8f5e9,stroke:#333,color:#333
    style K fill:#e8f5e9,stroke:#333,color:#333
```

## MCP Installation Methods

Claude Code supports multiple transport protocols for MCP server connections:

### HTTP Transport (Recommended)

```bash
# Basic HTTP connection
claude mcp add --transport http notion https://mcp.notion.com/mcp

# HTTP with authentication header
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

### Stdio Transport (Local)

For locally running MCP servers:

```bash
# Local Node.js server
claude mcp add --transport stdio myserver -- npx @myorg/mcp-server

# With environment variables
claude mcp add --transport stdio myserver --env KEY=value -- npx server
```

### SSE Transport (Deprecated)

Server-Sent Events transport is deprecated but still supported:

```bash
claude mcp add --transport sse legacy-server https://example.com/sse
```

### Windows-Specific Note

On native Windows (not WSL), use `cmd /c` for npx commands:

```bash
claude mcp add --transport stdio my-server -- cmd /c npx -y @some/package
```

### OAuth 2.0 Authentication

Claude Code supports OAuth 2.0 for MCP servers that require it. When connecting to an OAuth-enabled server, Claude Code handles the entire authentication flow:

```bash
# Connect to an OAuth-enabled MCP server (interactive flow)
claude mcp add --transport http my-service https://my-service.example.com/mcp

# Pre-configure OAuth credentials for non-interactive setup
claude mcp add --transport http my-service https://my-service.example.com/mcp \
  --client-id "your-client-id" \
  --client-secret "your-client-secret" \
  --callback-port 8080
```

| Feature | Description |
|---------|-------------|
| **Interactive OAuth** | Use `/mcp` to trigger the browser-based OAuth flow |
| **Pre-configured credentials** | `--client-id`, `--client-secret`, `--callback-port` flags for automated setup |
| **Token storage** | Tokens are stored securely in your system keychain |
| **Step-up auth** | Supports step-up authentication for privileged operations |
| **Discovery caching** | OAuth discovery metadata is cached for faster reconnections |

### Claude.ai MCP Connectors

MCP servers configured in your Claude.ai account are automatically available in Claude Code. This means any MCP connections you set up through the Claude.ai web interface will be accessible without additional configuration.

> **Note:** This feature is only available for users logged in with Claude.ai accounts.

## MCP Setup Process

```mermaid
sequenceDiagram
    participant User
    participant Claude as Claude Code
    participant Config as Config File
    participant Service as External Service

    User->>Claude: Type /mcp
    Claude->>Claude: List available MCP servers
    Claude->>User: Show options
    User->>Claude: Select GitHub MCP
    Claude->>Config: Update configuration
    Config->>Claude: Activate connection
    Claude->>Service: Test connection
    Service-->>Claude: Authentication successful
    Claude->>User: ✅ MCP connected!
```

## MCP Tool Search

When MCP tool descriptions exceed 10% of the context window, Claude Code automatically enables tool search to efficiently select the right tools without overwhelming the model context.

| Setting | Value | Description |
|---------|-------|-------------|
| `ENABLE_TOOL_SEARCH` | `auto` (default) | Automatically enables when tool descriptions exceed 10% of context |
| `ENABLE_TOOL_SEARCH` | `auto:<N>` | Automatically enables at a custom threshold of `N` tools |
| `ENABLE_TOOL_SEARCH` | `true` | Always enabled regardless of tool count |
| `ENABLE_TOOL_SEARCH` | `false` | Disabled; all tool descriptions sent in full |

> **Note:** Tool search requires Sonnet 4 or later, or Opus 4 or later. Haiku models are not supported for tool search.

## Dynamic Tool Updates

Claude Code supports MCP `list_changed` notifications. When an MCP server dynamically adds, removes, or modifies its available tools, Claude Code receives the update and adjusts its tool list automatically -- no reconnection or restart required.

## MCP Resources via @ Mentions

You can reference MCP resources directly in your prompts using the `@` mention syntax:

```
@server-name:protocol://resource/path
```

For example, to reference a specific database resource:

```
@database:postgres://mydb/users
```

This allows Claude to fetch and include MCP resource content inline as part of the conversation context.

## MCP Scopes

MCP configurations can be stored at different scopes with varying levels of sharing:

| Scope | Location | Description | Shared With | Requires Approval |
|-------|----------|-------------|-------------|------------------|
| **Local** (default) | `~/.claude.json` | Private to current user | Just you | No |
| **Project** | `.mcp.json` | Checked into git repository | Team members | Yes (first use) |
| **User** | `~/.claude.json` | Available across all projects | Just you | No |

### Using Project Scope

Store project-specific MCP configurations in `.mcp.json`:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp"
    }
  }
}
```

Team members will see an approval prompt on first use of project MCPs.

## MCP Configuration Management

### Adding MCP Servers

```bash
# Add HTTP-based server
claude mcp add --transport http github https://api.github.com/mcp

# Add local stdio server
claude mcp add --transport stdio database -- npx @company/db-server

# List all MCP servers
claude mcp list

# Get details on specific server
claude mcp get github

# Remove an MCP server
claude mcp remove github

# Reset project-specific approval choices
claude mcp reset-project-choices

# Import from Claude Desktop
claude mcp add-from-claude-desktop
```

## Available MCP Servers Table

| MCP Server | Purpose | Common Tools | Auth | Real-time |
|------------|---------|--------------|------|-----------|
| **Filesystem** | File operations | read, write, delete | OS permissions | ✅ Yes |
| **GitHub** | Repository management | list_prs, create_issue, push | OAuth | ✅ Yes |
| **Slack** | Team communication | send_message, list_channels | Token | ✅ Yes |
| **Database** | SQL queries | query, insert, update | Credentials | ✅ Yes |
| **Google Docs** | Document access | read, write, share | OAuth | ✅ Yes |
| **Asana** | Project management | create_task, update_status | API Key | ✅ Yes |
| **Stripe** | Payment data | list_charges, create_invoice | API Key | ✅ Yes |
| **Memory** | Persistent memory | store, retrieve, delete | Local | ❌ No |

## Practical Examples

### Example 1: GitHub MCP Configuration

**File:** `.mcp.json` (project root)

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**Available GitHub MCP Tools:**

#### Pull Request Management
- `list_prs` - List all PRs in repository
- `get_pr` - Get PR details including diff
- `create_pr` - Create new PR
- `update_pr` - Update PR description/title
- `merge_pr` - Merge PR to main branch
- `review_pr` - Add review comments

**Example request:**
```
/mcp__github__get_pr 456

# Returns:
Title: Add dark mode support
Author: @alice
Description: Implements dark theme using CSS variables
Status: OPEN
Reviewers: @bob, @charlie
```

#### Issue Management
- `list_issues` - List all issues
- `get_issue` - Get issue details
- `create_issue` - Create new issue
- `close_issue` - Close issue
- `add_comment` - Add comment to issue

#### Repository Information
- `get_repo_info` - Repository details
- `list_files` - File tree structure
- `get_file_content` - Read file contents
- `search_code` - Search across codebase

#### Commit Operations
- `list_commits` - Commit history
- `get_commit` - Specific commit details
- `create_commit` - Create new commit

**Setup**:
```bash
export GITHUB_TOKEN="your_github_token"
# Or use the CLI to add directly:
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github
```

### Environment Variable Expansion in Configuration

MCP configurations support environment variable expansion with fallback defaults. The `${VAR}` and `${VAR:-default}` syntax works in the following fields: `command`, `args`, `env`, `url`, and `headers`.

```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}",
        "X-Custom-Header": "${CUSTOM_HEADER:-default-value}"
      }
    },
    "local-server": {
      "command": "${MCP_BIN_PATH:-npx}",
      "args": ["${MCP_PACKAGE:-@company/mcp-server}"],
      "env": {
        "DB_URL": "${DATABASE_URL:-postgresql://localhost/dev}"
      }
    }
  }
}
```

Variables are expanded at runtime:
- `${VAR}` - Uses environment variable, error if not set
- `${VAR:-default}` - Uses environment variable, falls back to default if not set

### Example 2: Database MCP Setup

**Configuration:**

```json
{
  "mcpServers": {
    "database": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-database"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

**Example Usage:**

```markdown
User: Fetch all users with more than 10 orders

Claude: I'll query your database to find that information.

# Using MCP database tool:
SELECT u.*, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id
HAVING COUNT(o.id) > 10
ORDER BY order_count DESC;

# Results:
- Alice: 15 orders
- Bob: 12 orders
- Charlie: 11 orders
```

**Setup**:
```bash
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
# Or use the CLI to add directly:
claude mcp add --transport stdio database -- npx @modelcontextprotocol/server-database
```

### Example 3: Multi-MCP Workflow

**Scenario: Daily Report Generation**

```markdown
# Daily Report Workflow using Multiple MCPs

## Setup
1. GitHub MCP - fetch PR metrics
2. Database MCP - query sales data
3. Slack MCP - post report
4. Filesystem MCP - save report

## Workflow

### Step 1: Fetch GitHub Data
/mcp__github__list_prs completed:true last:7days

Output:
- Total PRs: 42
- Average merge time: 2.3 hours
- Review turnaround: 1.1 hours

### Step 2: Query Database
SELECT COUNT(*) as sales, SUM(amount) as revenue
FROM orders
WHERE created_at > NOW() - INTERVAL '1 day'

Output:
- Sales: 247
- Revenue: $12,450

### Step 3: Generate Report
Combine data into HTML report

### Step 4: Save to Filesystem
Write report.html to /reports/

### Step 5: Post to Slack
Send summary to #daily-reports channel

Final Output:
✅ Report generated and posted
📊 47 PRs merged this week
💰 $12,450 in daily sales
```

**Setup**:
```bash
export GITHUB_TOKEN="your_github_token"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
export SLACK_TOKEN="your_slack_token"
# Add each MCP server via the CLI or configure them in .mcp.json
```

### Example 4: Filesystem MCP Operations

**Configuration:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/home/user/projects"]
    }
  }
}
```

**Available Operations:**

| Operation | Command | Purpose |
|-----------|---------|---------|
| List files | `ls ~/projects` | Show directory contents |
| Read file | `cat src/main.ts` | Read file contents |
| Write file | `create docs/api.md` | Create new file |
| Edit file | `edit src/app.ts` | Modify file |
| Search | `grep "async function"` | Search in files |
| Delete | `rm old-file.js` | Delete file |

**Setup**:
```bash
# Use the CLI to add directly:
claude mcp add --transport stdio filesystem -- npx @modelcontextprotocol/server-filesystem /home/user/projects
```

## MCP vs Memory: Decision Matrix

```mermaid
graph TD
    A["Need external data?"]
    A -->|No| B["Use Memory"]
    A -->|Yes| C["Does it change frequently?"]
    C -->|No/Rarely| B
    C -->|Yes/Often| D["Use MCP"]

    B -->|Stores| E["Preferences<br/>Context<br/>History"]
    D -->|Accesses| F["Live APIs<br/>Databases<br/>Services"]

    style A fill:#fff3e0,stroke:#333,color:#333
    style B fill:#e1f5fe,stroke:#333,color:#333
    style C fill:#fff3e0,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#e8f5e9,stroke:#333,color:#333
    style F fill:#e8f5e9,stroke:#333,color:#333
```

## Request/Response Pattern

```mermaid
sequenceDiagram
    participant App as Claude
    participant MCP as MCP Server
    participant DB as Database

    App->>MCP: Request: "SELECT * FROM users WHERE id=1"
    MCP->>DB: Execute query
    DB-->>MCP: Result set
    MCP-->>App: Return parsed data
    App->>App: Process result
    App->>App: Continue task

    Note over MCP,DB: Real-time access<br/>No caching
```

## Environment Variables

Store sensitive credentials in environment variables:

```bash
# ~/.bashrc or ~/.zshrc
export GITHUB_TOKEN="ghp_xxxxxxxxxxxxx"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
export SLACK_TOKEN="xoxb-xxxxxxxxxxxxx"
```

Then reference them in MCP config:

```json
{
  "env": {
    "GITHUB_TOKEN": "${GITHUB_TOKEN}"
  }
}
```

## Claude as MCP Server (`claude mcp serve`)

Claude Code itself can act as an MCP server for other applications. This enables external tools, editors, and automation systems to leverage Claude's capabilities through the standard MCP protocol.

```bash
# Start Claude Code as an MCP server on stdio
claude mcp serve
```

Other applications can then connect to this server as they would any stdio-based MCP server. For example, to add Claude Code as an MCP server in another Claude Code instance:

```bash
claude mcp add --transport stdio claude-agent -- claude mcp serve
```

This is useful for building multi-agent workflows where one Claude instance orchestrates another.

## Managed MCP Configuration (Enterprise)

For enterprise deployments, IT administrators can enforce MCP server policies through the `managed-mcp.json` configuration file. This file provides exclusive control over which MCP servers are permitted or blocked organization-wide.

**Location:**
- macOS: `/Library/Application Support/ClaudeCode/managed-mcp.json`
- Linux: `~/.config/ClaudeCode/managed-mcp.json`
- Windows: `%APPDATA%\ClaudeCode\managed-mcp.json`

**Features:**
- `allowedMcpServers` -- whitelist of permitted servers
- `deniedMcpServers` -- blocklist of prohibited servers
- Supports matching by server name, command, and URL patterns
- Organization-wide MCP policies enforced before user configuration
- Prevents unauthorized server connections

**Example configuration:**

```json
{
  "allowedMcpServers": [
    {
      "serverName": "github",
      "serverUrl": "https://api.github.com/mcp"
    },
    {
      "serverName": "company-internal",
      "serverCommand": "company-mcp-server"
    }
  ],
  "deniedMcpServers": [
    {
      "serverName": "untrusted-*"
    },
    {
      "serverUrl": "http://*"
    }
  ]
}
```

> **Note:** When both `allowedMcpServers` and `deniedMcpServers` match a server, the deny rule takes precedence.

## Plugin-Provided MCP Servers

Plugins can bundle their own MCP servers, making them available automatically when the plugin is installed. Plugin-provided MCP servers can be defined in two ways:

1. **Standalone `.mcp.json`** -- Place a `.mcp.json` file in the plugin root directory
2. **Inline in `plugin.json`** -- Define MCP servers directly within the plugin manifest

Use the `${CLAUDE_PLUGIN_ROOT}` variable to reference paths relative to the plugin's installation directory:

```json
{
  "mcpServers": {
    "plugin-tools": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/dist/mcp-server.js"],
      "env": {
        "CONFIG_PATH": "${CLAUDE_PLUGIN_ROOT}/config.json"
      }
    }
  }
}
```

## MCP Output Limits

Claude Code enforces limits on MCP tool output to prevent context overflow:

| Limit | Threshold | Behavior |
|-------|-----------|----------|
| **Warning** | 10,000 tokens | A warning is displayed that the output is large |
| **Default max** | 25,000 tokens | Output is truncated beyond this limit |
| **Disk persistence** | 50,000 characters | Tool results exceeding 50K characters are persisted to disk |

The maximum output limit is configurable via the `MAX_MCP_OUTPUT_TOKENS` environment variable:

```bash
# Increase the max output to 50,000 tokens
export MAX_MCP_OUTPUT_TOKENS=50000
```

## Best Practices

### Security Considerations

#### Do's ✅
- Use environment variables for all credentials
- Rotate tokens and API keys regularly (monthly recommended)
- Use read-only tokens when possible
- Limit MCP server access scope to minimum required
- Monitor MCP server usage and access logs
- Use OAuth for external services when available
- Implement rate limiting on MCP requests
- Test MCP connections before production use
- Document all active MCP connections
- Keep MCP server packages updated

#### Don'ts ❌
- Don't hardcode credentials in config files
- Don't commit tokens or secrets to git
- Don't share tokens in team chats or emails
- Don't use personal tokens for team projects
- Don't grant unnecessary permissions
- Don't ignore authentication errors
- Don't expose MCP endpoints publicly
- Don't run MCP servers with root/admin privileges
- Don't cache sensitive data in logs
- Don't disable authentication mechanisms

### Configuration Best Practices

1. **Version Control**: Keep `.mcp.json` in git but use environment variables for secrets
2. **Least Privilege**: Grant minimum permissions needed for each MCP server
3. **Isolation**: Run different MCP servers in separate processes when possible
4. **Monitoring**: Log all MCP requests and errors for audit trails
5. **Testing**: Test all MCP configurations before deploying to production

### Performance Tips

- Cache frequently accessed data at the application level
- Use MCP queries that are specific to reduce data transfer
- Monitor response times for MCP operations
- Consider rate limiting for external APIs
- Use batching when performing multiple operations

## Installation Instructions

### Prerequisites
- Node.js and npm installed
- Claude Code CLI installed
- API tokens/credentials for external services

### Step-by-Step Setup

1. **Add your first MCP server** using the CLI (example: GitHub):
```bash
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github
```

   Or create a `.mcp.json` file in your project root:
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

2. **Set environment variables:**
```bash
export GITHUB_TOKEN="your_github_personal_access_token"
```

3. **Test the connection:**
```bash
claude /mcp
```

4. **Use MCP tools:**
```bash
/mcp__github__list_prs
/mcp__github__create_issue "Title" "Description"
```

### Installation for Specific Services

**GitHub MCP:**
```bash
npm install -g @modelcontextprotocol/server-github
```

**Database MCP:**
```bash
npm install -g @modelcontextprotocol/server-database
```

**Filesystem MCP:**
```bash
npm install -g @modelcontextprotocol/server-filesystem
```

**Slack MCP:**
```bash
npm install -g @modelcontextprotocol/server-slack
```

## Troubleshooting

### MCP Server Not Found
```bash
# Verify MCP server is installed
npm list -g @modelcontextprotocol/server-github

# Install if missing
npm install -g @modelcontextprotocol/server-github
```

### Authentication Failed
```bash
# Verify environment variable is set
echo $GITHUB_TOKEN

# Re-export if needed
export GITHUB_TOKEN="your_token"

# Verify token has correct permissions
# Check GitHub token scopes at: https://github.com/settings/tokens
```

### Connection Timeout
- Check network connectivity: `ping api.github.com`
- Verify API endpoint is accessible
- Check rate limits on API
- Try increasing timeout in config
- Check for firewall or proxy issues

### MCP Server Crashes
- Check MCP server logs: `~/.claude/logs/`
- Verify all environment variables are set
- Ensure proper file permissions
- Try reinstalling the MCP server package
- Check for conflicting processes on the same port

## Related Concepts

### Memory vs MCP
- **Memory**: Stores persistent, unchanging data (preferences, context, history)
- **MCP**: Accesses live, changing data (APIs, databases, real-time services)

### When to Use Each
- **Use Memory** for: User preferences, conversation history, learned context
- **Use MCP** for: Current GitHub issues, live database queries, real-time data

### Integration with Other Claude Features
- Combine MCP with Memory for rich context
- Use MCP tools in prompts for better reasoning
- Leverage multiple MCPs for complex workflows

## Additional Resources

- [Official MCP Documentation](https://code.claude.com/docs/en/mcp)
- [MCP Protocol Specification](https://modelcontextprotocol.io/specification)
- [MCP GitHub Repository](https://github.com/modelcontextprotocol/servers)
- [Available MCP Servers](https://github.com/modelcontextprotocol/servers)
- [Claude Code CLI Reference](https://code.claude.com/docs/en/cli-reference)
- [Claude API Documentation](https://docs.anthropic.com)

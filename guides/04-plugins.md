# Plugins (MCP Servers)

Claude Code extends its capabilities through MCP (Model Context Protocol) plugins. These give Claude access to external tools and services.

## Recommended Plugins

### GitHub (MUST HAVE)

Access GitHub issues, pull requests, code search, and repository management directly from Claude.

```bash
claude mcp add github -- npx -y @anthropic-ai/mcp-remote@latest https://api.githubcopilot.com/mcp/
```

**What it enables:**
- Read and create issues
- Create and review pull requests
- Search code across repositories
- Manage branches and releases

### Context7 (MUST HAVE)

Get up-to-date documentation for any library, directly in context.

```bash
claude mcp add context7 -- npx -y @anthropic-ai/mcp-remote@latest https://mcp.context7.com/mcp
```

**What it enables:**
- Look up current API docs for any npm/pip/maven package
- No more hallucinated APIs -- Claude checks real documentation
- Especially useful for fast-moving libraries

### Claude Memory (RECOMMENDED)

Persistent cross-session memory. Claude remembers decisions, patterns, and context across conversations.

```bash
claude mcp add claude-mem -- npx -y @anthropic-ai/mcp-remote@latest https://mcp.claude-mem.com/mcp
```

**What it enables:**
- Save important decisions and patterns
- Search past work across sessions
- Build up project knowledge over time
- Avoid re-discovering the same things

### Playwright (OPTIONAL)

Browser automation for testing and debugging web applications.

```bash
claude mcp add playwright -- npx -y @anthropic-ai/mcp-remote@latest https://mcp.playwright.dev/mcp
```

**What it enables:**
- Navigate web pages, fill forms, click buttons
- Take screenshots for visual verification
- Run browser-based tests
- Debug frontend issues

## Plugin Management

```bash
# List installed plugins
claude mcp list

# Remove a plugin
claude mcp remove <name>

# Plugin configs are stored in ~/.claude.json
```

## Tips

- Plugins are available in all conversations automatically
- They consume context when used, so Claude will only invoke them when relevant
- You can install additional MCP servers -- check [modelcontextprotocol.io](https://modelcontextprotocol.io) for the registry

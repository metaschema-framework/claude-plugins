# Contributing to Metaschema Framework Claude Plugins

Thank you for your interest in contributing! This guide explains how to add new plugins or improve existing ones.

## Plugin Structure

Each plugin follows the standard Claude Code plugin structure:

```
plugins/plugin-name/
├── .claude-plugin/
│   └── plugin.json      # Plugin manifest (required)
├── commands/            # Slash commands (optional)
│   └── command-name.md
├── skills/              # Agent skills (optional)
│   └── skill-name/
│       └── SKILL.md
├── agents/              # Subagent definitions (optional)
│   └── agent-name.md
├── hooks/               # Hook configurations (optional)
│   └── hooks.json
├── .mcp.json            # MCP server config (optional)
└── README.md            # Plugin documentation (required)
```

## Creating a New Plugin

### 1. Create the Plugin Directory

```bash
mkdir -p plugins/my-plugin/.claude-plugin
mkdir -p plugins/my-plugin/skills
mkdir -p plugins/my-plugin/commands
mkdir -p plugins/my-plugin/agents
```

### 2. Create plugin.json

```json
{
  "name": "my-plugin",
  "description": "Brief description of what this plugin does",
  "version": "0.1.0",
  "author": {
    "name": "Your Name",
    "email": "your.email@example.com"
  },
  "repository": "https://github.com/metaschema-framework/claude-plugins",
  "license": "Apache-2.0",
  "keywords": ["relevant", "keywords"]
}
```

### 3. Add to Marketplace

Update `.claude-plugin/marketplace.json` to include your plugin:

```json
{
  "plugins": {
    "my-plugin": {
      "path": "./plugins/my-plugin",
      "description": "Brief description"
    }
  }
}
```

### 4. Create README.md

Document your plugin with:
- What it does
- How to use it
- Any requirements or dependencies

## Component Guidelines

### Skills (SKILL.md)

Skills provide Claude with knowledge and workflows. Create `skills/skill-name/SKILL.md`:

```markdown
---
name: skill-name
description: When Claude should use this skill
---

# Skill Title

Instructions for Claude when this skill is activated...
```

### Commands (command-name.md)

Slash commands users can invoke. Create `commands/command-name.md`:

```markdown
---
description: What this command does
---

# Command Name

Instructions for executing this command with arguments: $ARGUMENTS
```

### Agents (agent-name.md)

Specialized subagents. Create `agents/agent-name.md`:

```markdown
---
description: What this agent specializes in
---

# Agent Name

Detailed description of the agent's role and expertise...
```

### MCP Servers (.mcp.json)

For CLI tool integration:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "path/to/command",
      "args": ["--flag", "value"],
      "env": {
        "ENV_VAR": "value"
      }
    }
  }
}
```

## Testing Your Plugin

Test locally before submitting:

```bash
# Load plugin directly
claude --plugin-dir ./plugins/my-plugin

# Verify it loads
claude plugin list
```

## Submitting Changes

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-plugin`
3. Make your changes
4. Test your plugin locally
5. Commit with clear messages
6. Submit a pull request

## Code of Conduct

- Be respectful and constructive
- Focus on the technical merits
- Help others learn and improve

## Questions?

Open an issue for questions or discussions about plugin development.

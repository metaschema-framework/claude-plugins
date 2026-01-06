# Claude Plugins Repository Design

## Overview

A public community monorepo for Claude Code plugins focused on Metaschema and OSCAL development. Each plugin is independently installable via the Claude plugin marketplace system.

## Repository Structure

```
claude-plugins/
├── README.md                    # Overview, installation, contributing
├── LICENSE                      # Apache-2.0 (existing)
├── CONTRIBUTING.md              # Contribution guidelines
├── docs/
│   └── plans/                   # Design documents
├── .claude-plugin/
│   └── marketplace.json         # Marketplace index for all plugins
│
└── plugins/
    ├── metaschema/              # Metaschema knowledge/skills
    ├── metaschema-tools/        # Metaschema tooling
    ├── oscal/                   # OSCAL knowledge/skills
    └── oscal-tools/             # OSCAL tooling
```

## Plugins

### metaschema

Knowledge plugin for understanding and working with Metaschema specifications.

**Contents:**
- `skills/` - Understanding metaschema syntax, constraints, datatypes
- `agents/` - Metaschema expert for answering questions

### metaschema-tools

Tooling plugin for metaschema-java CLI integration.

**Contents:**
- `commands/` - Slash commands for validation, schema generation
- `skills/` - Skills for using the tools effectively
- `agents/` - Tool-specialized agents
- `.mcp.json` - MCP server wrapping metaschema-java CLI

### oscal

Knowledge plugin for understanding OSCAL models and security concepts.

**Contents:**
- `skills/` - Understanding OSCAL layers (catalog, profile, component, SSP, etc.)
- `agents/` - OSCAL/security compliance expert

### oscal-tools

Tooling plugin for oscal-cli integration.

**Contents:**
- `commands/` - Slash commands for validation, profile resolution, conversion
- `skills/` - Skills for using the tools effectively
- `agents/` - Tool-specialized agents
- `.mcp.json` - MCP server wrapping oscal-cli

## Plugin Structure

Each plugin follows the standard Claude plugin structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json      # Plugin manifest (required)
├── commands/            # Slash commands (optional)
├── skills/              # Agent skills (optional)
├── agents/              # Subagent definitions (optional)
├── hooks/               # Hook configurations (optional)
├── .mcp.json            # MCP server config (optional)
└── README.md            # Plugin documentation
```

## Installation

Users install plugins via:

```bash
# Add the marketplace (one-time)
claude plugin install gh:metaschema-framework/claude-plugins

# Install specific plugins
claude plugin install metaschema-framework:metaschema
claude plugin install metaschema-framework:metaschema-tools
claude plugin install metaschema-framework:oscal
claude plugin install metaschema-framework:oscal-tools
```

## Marketplace Configuration

The root `.claude-plugin/marketplace.json` indexes all available plugins:

```json
{
  "name": "metaschema-framework",
  "description": "Claude plugins for Metaschema and OSCAL development",
  "plugins": {
    "metaschema": { "path": "./plugins/metaschema" },
    "metaschema-tools": { "path": "./plugins/metaschema-tools" },
    "oscal": { "path": "./plugins/oscal" },
    "oscal-tools": { "path": "./plugins/oscal-tools" }
  }
}
```

## Design Decisions

1. **Monorepo over separate repos** - Easier discovery, consistent versioning, shared contribution guidelines
2. **Knowledge vs Tools split** - Separates conceptual understanding from CLI tooling; users can install just what they need
3. **Standard plugin structure** - Follows official Claude plugin conventions for compatibility
4. **Marketplace-based distribution** - Leverages Claude's built-in plugin installation system

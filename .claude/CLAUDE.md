# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **Claude Code plugin marketplace** for Metaschema and OSCAL development. It contains 5 plugins that provide skills, commands, and agents for working with the Metaschema framework and OSCAL security standards.

## Architecture

```
.claude-plugin/marketplace.json    # Marketplace manifest (pluginRoot: ./plugins)
plugins/
├── metaschema/                    # Metaschema specification knowledge
├── metaschema-tools/              # metaschema-java CLI integration
├── oscal/                         # OSCAL security model knowledge
├── oscal-tools/                   # oscal-cli integration
└── dev-metaschema/                # Development workflow for contributors
examples/                          # Sample OSCAL documents (XML, YAML, JSON)
```

Each plugin follows this structure:
```
plugins/{name}/
├── .claude-plugin/plugin.json     # Plugin manifest with version
├── skills/{skill-name}/SKILL.md   # Auto-discovered skills
├── commands/{name}.md             # Slash commands
├── agents/{name}.md               # Subagent definitions
└── README.md
```

## Plugin Relationships

- **Knowledge plugins** (`metaschema`, `oscal`): Provide domain expertise via skills
- **Tools plugins** (`metaschema-tools`, `oscal-tools`): Provide CLI integration, reference knowledge plugins
- **Dev plugin** (`dev-metaschema`): Development workflows for framework contributors

## Version Management

When modifying plugin content, update the version in `plugins/{name}/.claude-plugin/plugin.json`:
- **Patch (0.0.X)**: Bug fixes, typos
- **Minor (0.X.0)**: New skills/commands/features
- **Major (X.0.0)**: Breaking changes

The `marketplace.json` does NOT store versions - each plugin's `plugin.json` is authoritative.

## Testing Plugins

```bash
# Load plugin directly for testing
claude --plugin-dir ./plugins/my-plugin

# Verify plugin loads
claude plugin list
```

## Validating OSCAL Examples

```bash
# Using oscal-cli (preferred syntax without model prefix)
oscal-cli validate examples/profile/sample-profile.yaml
oscal-cli convert --to=json examples/profile/sample-profile.yaml output.json
oscal-cli resolve-profile --to=json profile.yaml resolved-catalog.json
```

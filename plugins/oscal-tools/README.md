# OSCAL Tools Plugin

Commands and MCP servers for [oscal-cli](https://github.com/metaschema-framework/oscal-cli) integration.

## Installation

```bash
claude plugin install metaschema-framework:oscal-tools
```

## Requirements

- [oscal-cli](https://github.com/metaschema-framework/oscal-cli) installed and available in PATH

## Features

### Commands

Slash commands for common oscal-cli operations:

- OSCAL document validation
- Profile resolution
- Format conversion (JSON, XML, YAML)

### MCP Server

Direct integration with oscal-cli for:

- Validating OSCAL documents against schemas
- Resolving profiles to produce resolved catalogs
- Converting between OSCAL formats
- Querying OSCAL content

### Skills

- Effective use of oscal-cli tooling
- Understanding CLI options and workflows

## Usage

Once installed, use the provided slash commands or let Claude invoke the MCP server directly.

## Related Plugins

This plugin references skills from the **oscal** plugin for OSCAL conceptual knowledge. Consider installing both:

```bash
claude plugin install metaschema-framework:oscal
claude plugin install metaschema-framework:oscal-tools
```

## Resources

- [oscal-cli Documentation](https://github.com/metaschema-framework/oscal-cli)

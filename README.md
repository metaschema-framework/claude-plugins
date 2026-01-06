# Metaschema Framework Claude Plugins

Claude Code plugins for working with [Metaschema](https://github.com/metaschema-framework/metaschema) and [OSCAL](https://pages.nist.gov/OSCAL/) (Open Security Controls Assessment Language).

## Available Plugins

| Plugin | Description |
|--------|-------------|
| `metaschema` | Skills and agents for understanding Metaschema specifications |
| `metaschema-tools` | Commands and MCP servers for metaschema-java CLI integration |
| `oscal` | Skills and agents for understanding OSCAL security models |
| `oscal-tools` | Commands and MCP servers for oscal-cli integration |

## Installation

### Add the Marketplace

```bash
claude plugin install gh:metaschema-framework/claude-plugins
```

### Install Plugins

Install the plugins you need:

```bash
# Metaschema plugins
claude plugin install metaschema-framework:metaschema
claude plugin install metaschema-framework:metaschema-tools

# OSCAL plugins
claude plugin install metaschema-framework:oscal
claude plugin install metaschema-framework:oscal-tools
```

### Verify Installation

```bash
claude plugin list
```

## Plugin Details

### metaschema

Knowledge plugin providing Claude with deep understanding of Metaschema:

- **Skills**: Metaschema syntax, constraints, datatypes, module composition
- **Agents**: Metaschema expert for answering specification questions

### metaschema-tools

Tooling plugin integrating [metaschema-java](https://github.com/metaschema-framework/metaschema-java):

- **Commands**: Schema validation, code generation
- **MCP Server**: Direct integration with metaschema-java CLI
- **Skills**: Effective use of metaschema tooling

### oscal

Knowledge plugin providing Claude with deep understanding of OSCAL:

- **Skills**: OSCAL layers (catalog, profile, component-definition, SSP, SAP, SAR, POA&M)
- **Agents**: OSCAL/security compliance expert

### oscal-tools

Tooling plugin integrating [oscal-cli](https://github.com/metaschema-framework/oscal-cli):

- **Commands**: OSCAL validation, profile resolution, format conversion
- **MCP Server**: Direct integration with oscal-cli
- **Skills**: Effective use of OSCAL tooling

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI
- For tools plugins:
  - `metaschema-tools`: [metaschema-java](https://github.com/metaschema-framework/metaschema-java) installed
  - `oscal-tools`: [oscal-cli](https://github.com/metaschema-framework/oscal-cli) installed

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on contributing new plugins or improving existing ones.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Related Projects

- [Metaschema](https://github.com/metaschema-framework/metaschema) - Information modeling framework
- [metaschema-java](https://github.com/metaschema-framework/metaschema-java) - Java implementation
- [OSCAL](https://pages.nist.gov/OSCAL/) - Open Security Controls Assessment Language
- [oscal-cli](https://github.com/metaschema-framework/oscal-cli) - OSCAL command-line tool

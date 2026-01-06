---
name: using-metaschema-java
description: Use when running metaschema-java CLI commands for validation, schema generation, and conversion
---

# Using metaschema-java CLI

The metaschema-java CLI provides command-line tools for working with Metaschema modules.

→ **For programmatic Java usage:** See `metaschema-java-library` skill

## Common Commands

### Validate a Metaschema Module

```bash
metaschema-cli validate <metaschema-file>
```

Validates the Metaschema module syntax and structure.

### Validate Content Against a Metaschema

```bash
metaschema-cli validate-content --metaschema <metaschema-file> <content-file>
```

Validates XML or JSON content against the constraints defined in a Metaschema module.

### Generate XML Schema

```bash
metaschema-cli generate-schema --as xml <metaschema-file>
```

### Generate JSON Schema

```bash
metaschema-cli generate-schema --as json <metaschema-file>
```

### Convert Content Between Formats

```bash
metaschema-cli convert --metaschema <metaschema-file> --to xml <json-file>
metaschema-cli convert --metaschema <metaschema-file> --to json <xml-file>
```

Converts content between XML and JSON formats using the Metaschema as the model definition.

## Common Options

| Option | Description |
|--------|-------------|
| `--as <format>` | Specify output format (xml, json) |
| `-o <file>` | Write output to file |
| `--help` | Show command help |

## Best Practices

1. Always validate Metaschema modules before generating schemas
2. Validate content against Metaschema constraints, not just schema
3. Use `--help` on any command to see available options

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Validation errors | Check module imports and namespace declarations |
| Content validation failures | Review constraint violations in output |
| Schema generation issues | Verify all referenced modules are accessible |
| Conversion errors | Ensure source content is valid before converting |

## Related Skills

| Task | Skill |
|------|-------|
| CLI commands (you are here) | `using-metaschema-java` |
| Java library/API usage | `metaschema-java-library` |
| Understanding Metaschema | `metaschema-basics` (from `metaschema` plugin) |

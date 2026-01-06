---
name: using-oscal-cli
description: Use when running oscal-cli commands or working with OSCAL tooling
---

# Using oscal-cli

The oscal-cli provides tools for validating, converting, and processing OSCAL documents.

## Common Commands

### Validate an OSCAL Document

```bash
oscal-cli validate <oscal-file>
```

Validates against the appropriate OSCAL schema based on document type.

### Convert Between Formats

```bash
oscal-cli convert --to json <oscal-xml-file>
oscal-cli convert --to xml <oscal-json-file>
oscal-cli convert --to yaml <oscal-file>
```

### Resolve a Profile

```bash
oscal-cli resolve-profile <profile-file> -o <output-catalog>
```

Produces a resolved catalog from a profile and its imported catalogs.

## Validation Options

- `--as <format>`: Specify input format (xml, json, yaml)
- `--sarif`: Output results in SARIF format
- `-o <file>`: Write output to file

## Best Practices

1. Always validate OSCAL documents before processing
2. Use profile resolution to produce baseline catalogs
3. Convert to JSON for programmatic processing
4. Use XML for human-readable documentation

## Troubleshooting

- **Validation errors**: Check document structure matches OSCAL schema
- **Profile resolution failures**: Verify all imported catalogs/profiles are accessible
- **Conversion issues**: Ensure source document is valid first

## Related Skills

| Task | Skill |
|------|-------|
| CLI commands (you are here) | `using-oscal-cli` |
| Understanding OSCAL | `oscal-basics` (from `oscal` plugin) |

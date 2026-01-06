---
name: using-oscal-cli
description: Use when running oscal-cli commands or working with OSCAL tooling
---

# Using oscal-cli

The oscal-cli provides tools for validating, converting, and processing OSCAL documents.

## Commands Overview

| Command | Description |
|---------|-------------|
| `validate` | Check that an OSCAL instance is well-formed and valid |
| `convert` | Convert OSCAL between formats (XML, JSON, YAML) |
| `resolve-profile` | Resolve profile imports to produce a catalog |
| `list-allowed-values` | List allowed value constraints for OSCAL models |

## Validate

Check that an OSCAL document is well-formed and valid:

```bash
oscal-cli validate <file-or-URI>
```

### Options

| Option | Description |
|--------|-------------|
| `--as <FORMAT>` | Source format: XML, JSON, or YAML |
| `-c <URL>` | Additional constraint definitions |
| `-o <FILE>` | Write SARIF results to file |
| `--sarif-include-pass` | Include pass results in SARIF output |
| `--disable-schema-validation` | Skip schema validation |
| `--disable-constraint-validation` | Skip constraint validation |
| `--path-format <FORMAT>` | Path format: auto, metapath, xpath, jsonpointer |
| `--threads <count>` | Parallel constraint validation threads (experimental) |

### Examples

```bash
# Validate any OSCAL document (auto-detects model type)
oscal-cli validate ssp.json

# Validate with explicit format
oscal-cli validate document.xml --as xml

# Output SARIF results
oscal-cli validate catalog.json -o results.sarif

# Add custom constraints
oscal-cli validate ssp.json -c custom-constraints.xml
```

## Convert

Convert OSCAL documents between formats:

```bash
oscal-cli convert --to=<FORMAT> <source-file> [destination-file]
```

### Options

| Option | Description |
|--------|-------------|
| `--to <FORMAT>` | Target format: XML, JSON, or YAML (required) |
| `--overwrite` | Overwrite destination if it exists |

### Examples

```bash
# Convert JSON to YAML
oscal-cli convert --to=yaml catalog.json catalog.yaml

# Convert XML to JSON
oscal-cli convert --to=json ssp.xml ssp.json

# Convert to YAML (output to stdout if no destination)
oscal-cli convert --to=yaml profile.json
```

## Resolve Profile

Resolve a profile's imports to produce a resolved catalog:

```bash
oscal-cli resolve-profile --to=<FORMAT> <profile-URI> [destination-file]
```

### Options

| Option | Description |
|--------|-------------|
| `--as <FORMAT>` | Source format: XML, JSON, or YAML |
| `--to <FORMAT>` | Output format: XML, JSON, or YAML (required) |
| `--overwrite` | Overwrite destination if it exists |
| `--relative-to <URI>` | Generate URI references relative to this resource |
| `--pretty-print` | Enable pretty-printing for readability |

### Examples

```bash
# Resolve profile to JSON catalog
oscal-cli resolve-profile --to=json profile.json resolved-catalog.json

# Resolve with pretty printing
oscal-cli resolve-profile --to=json --pretty-print profile.xml output.json
```

## List Allowed Values

List allowed value constraints for OSCAL models:

```bash
oscal-cli list-allowed-values [destination-file]
```

### Options

| Option | Description |
|--------|-------------|
| `-c <URL>` | Additional constraint definitions |
| `--overwrite` | Overwrite destination if it exists |

## Global Options

Available for all commands:

| Option | Description |
|--------|-------------|
| `-h, --help` | Display help message |
| `--no-color` | Disable colorized output |
| `-q, --quiet` | Minimize output to errors only |
| `--show-stack-trace` | Show stack trace on errors |
| `--version` | Display application version |

## Common Workflows

### Validate Before Converting

```bash
oscal-cli validate document.json && oscal-cli convert --to=yaml document.json document.yaml
```

### Profile Resolution Pipeline

```bash
# 1. Validate the profile
oscal-cli validate profile.json

# 2. Resolve to catalog
oscal-cli resolve-profile --to=json profile.json resolved.json

# 3. Validate the resolved catalog
oscal-cli validate resolved.json
```

### Batch Validation

```bash
# Validate all JSON files in directory
for f in *.json; do oscal-cli validate "$f"; done
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Validation errors | Check document structure against OSCAL schema |
| Profile resolution failures | Verify all imported catalogs/profiles are accessible |
| Conversion issues | Validate source document first |
| Format detection fails | Use `--as` to specify source format explicitly |

## Related Skills

| Task | Skill |
|------|-------|
| Understanding OSCAL models | `oscal-basics` (oscal plugin) |
| Working with catalogs | `oscal-catalog` (oscal plugin) |
| Working with profiles | `oscal-profile` (oscal plugin) |
| Working with SSPs | `oscal-ssp` (oscal plugin) |

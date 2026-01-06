---
name: metaschema-basics
description: Use as an introduction to Metaschema concepts - provides overview and routes to detailed skills
---

# Metaschema Basics

Metaschema is an information modeling framework that allows defining data models in a format-agnostic way, generating XML and JSON schemas from a single source, and specifying constraints and validation rules.

## Core Concepts

### Module
A Metaschema file defining a model or portion of a model. Modules can import other modules.

→ **For details:** See `metaschema-module-authoring` skill (creating or understanding modules)

### Definition Types

| Type | Description | Example |
|------|-------------|---------|
| **Assembly** | Complex structure containing fields and other assemblies | `catalog`, `control`, `metadata` |
| **Field** | Simple data element with a typed value | `title`, `description`, `email` |
| **Flag** | Attribute/property on an assembly or field | `id`, `uuid`, `class` |

→ **For details:** See `metaschema-module-authoring` skill (structure, data types, cardinality)

### Constraints
Validation rules applied to data instances:
- `allowed-values` - Enumerated value sets
- `expect` - Assertions that must be true
- `matches` - Regex and datatype validation
- `has-cardinality` - Occurrence count rules
- `index` / `index-has-key` - Cross-reference validation
- `is-unique` - Uniqueness enforcement

→ **For details:** See `metaschema-constraints-authoring` skill (creating or understanding constraints)

### Metapath
XPath 3.1-based expression language for navigating and querying Metaschema data:

```text
./control[@id = 'AC-1']              # Find control by ID
.//param[exists(./value)]            # Find params with values
ancestor::catalog/metadata/title     # Navigate to catalog title
```

→ **For details:** See `metapath-expressions` skill (syntax, operators, functions)

## Data Formats

Metaschema supports three equivalent serialization formats:
- **XML** (`.xml`)
- **JSON** (`.json`)
- **YAML** (`.yaml`)

A model defined in Metaschema can parse and serialize to any of these formats.

## When to Use Which Skill

| Task | Skill |
|------|-------|
| Understanding or writing modules | `metaschema-module-authoring` |
| Understanding or writing constraints | `metaschema-constraints-authoring` |
| Understanding or writing Metapath expressions | `metapath-expressions` |
| Quick conceptual overview (you are here) | `metaschema-basics` |

## References

- [Metaschema Specification](https://pages.nist.gov/metaschema/)
- [Metaschema Tutorial](https://pages.nist.gov/metaschema/tutorials/)

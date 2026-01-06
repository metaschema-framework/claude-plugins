# Metaschema Plugin

Skills and agents for understanding and working with [Metaschema](https://github.com/metaschema-framework/metaschema) specifications.

## Installation

```bash
claude plugin install metaschema-framework:metaschema
```

## Skills

### metaschema-basics

Introduction and conceptual overview of Metaschema:

- Core concepts (modules, assemblies, fields, flags)
- Constraint types
- Data formats (XML, JSON, YAML)
- Routes to detailed skills

### metapath-expressions

Comprehensive guide to the Metapath expression language (based on XPath 3.1):

- Path expressions and axis navigation
- Predicates and filtering
- Operators (comparison, logical, arithmetic)
- 50+ built-in functions (string, numeric, sequence, date/time, array, map)
- Common expression patterns for constraints

### metaschema-constraints-authoring

Guide to creating and understanding Metaschema constraints:

- Constraint types: `allowed-values`, `expect`, `matches`, `has-cardinality`, `index`, `is-unique`
- Inline vs external constraints
- Metapath expressions in constraints
- Best practices and common patterns

### metaschema-module-authoring

Guide to authoring and understanding Metaschema modules:

- Module structure (YAML and XML formats)
- Definition types: assemblies, fields, flags
- Data types and cardinality
- JSON/XML-specific features
- Module imports and scope control

## Usage

Once installed, Claude will automatically use these skills when working with Metaschema-related tasks.

## Related

- [metaschema-tools](../metaschema-tools) - CLI tooling integration
- [Metaschema Specification](https://pages.nist.gov/metaschema/)

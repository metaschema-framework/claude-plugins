---
name: metaschema-constraints-authoring
description: Use when creating or modifying Metaschema constraints - covers all constraint types, inline vs external constraints, Metapath expressions, and validation patterns
---

# Metaschema Constraints Authoring

This skill guides the creation of Metaschema constraints for validating document instances. Constraints can be defined inline within definitions or externally in constraint modules.

## Constraint Types Overview

| Constraint | Allowed On | Purpose |
|------------|------------|---------|
| `allowed-values` | flag, field, assembly | Restrict values to enumerated set |
| `expect` | flag, field, assembly | Assert Metapath condition is true (fires when FALSE) |
| `report` | flag, field, assembly | Report when Metapath condition is true (fires when TRUE) |
| `matches` | flag, field, assembly | Validate against regex and/or datatype |
| `has-cardinality` | assembly only | Enforce occurrence counts |
| `index` | assembly only | Create named index with composite keys |
| `index-has-key` | flag, field, assembly | Validate cross-references against index |
| `is-unique` | assembly only | Ensure unique composite keys |
| `let` | all | Define reusable variables |

## Common Constraint Attributes

| Attribute | Values | Default | Description |
|-----------|--------|---------|-------------|
| `id` | token | none | Constraint identifier |
| `level` | CRITICAL, ERROR, WARNING, INFORMATIONAL, DEBUG | ERROR | Violation severity |
| `target` | Metapath | `.` (flags/fields) | Node selection expression |

## Inline Constraints

Define constraints directly within flag, field, or assembly definitions.

### YAML Syntax

```yaml
- object-type: flag
  name: status
  as-type: token
  constraint:
    rules:
    - object-type: allowed-values
      enums:
      - value: active
        remark: The item is active.
      - value: inactive
        remark: The item is inactive.
      - value: pending
        remark: The item is pending approval.
```

### XML Syntax

```xml
<define-flag name="status" as-type="token">
  <constraint>
    <allowed-values>
      <enum value="active">The item is active.</enum>
      <enum value="inactive">The item is inactive.</enum>
      <enum value="pending">The item is pending approval.</enum>
    </allowed-values>
  </constraint>
</define-flag>
```

## Constraint Types in Detail

### allowed-values

Restricts values to an enumerated set.

**Attributes:**
- `allow-other`: `yes` or `no` (default: `no`) - permit values beyond enumerated set
- `extensible`: `model`, `external`, or `none` (default: `external`) - allow constraint extension
- `target`: Required for fields/assemblies

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: allowed-values
    allow-other: "no"
    extensible: external
    target: "./format"
    enums:
    - value: XML
      remark: XML format.
    - value: JSON
      remark: JSON format.
    - value: YAML
      remark: YAML format.
```

**XML Example:**
```xml
<constraint>
  <allowed-values allow-other="no" extensible="external" target="./format">
    <enum value="XML">XML format.</enum>
    <enum value="JSON">JSON format.</enum>
    <enum value="YAML">YAML format.</enum>
  </allowed-values>
</constraint>
```

**Deprecating Values:**
```yaml
enums:
- value: new-value
  remark: Current preferred value.
- value: old-value
  deprecated: "1.0.0"
  remark: Deprecated, use new-value instead.
```

### expect

Validates that a Metapath expression evaluates to `true`.

**Attributes:**
- `test`: Required - Metapath boolean expression
- `target`: Required for fields/assemblies
- `message`: Optional - custom error message with Metapath templates

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: expect
    target: "."
    test: "string-length(.) > 0"
    message: "The value must not be empty."
  - object-type: expect
    target: "./start-date"
    test: ". <= ../end-date"
    message: "Start date ({ . }) must be before or equal to end date ({ ../end-date })."
```

**XML Example:**
```xml
<constraint>
  <expect target="." test="string-length(.) gt 0">
    <message>The value must not be empty.</message>
  </expect>
  <expect target="./start-date" test=". le ../end-date">
    <message>Start date ({ . }) must be before or equal to end date ({ ../end-date }).</message>
  </expect>
</constraint>
```

### report

Reports a finding when a Metapath expression evaluates to `true`. This is the inverse of `expect`: `expect` fires when the test is FALSE (assertion failed), while `report` fires when the test is TRUE (condition detected).

**Use cases:**
- Deprecation warnings (element is deprecated)
- Informational notices (condition detected that user should know about)
- Style suggestions (valid but not preferred)
- Audit findings (specific pattern detected)

**Attributes:**
- `test`: Required - Metapath boolean expression
- `target`: Required for fields/assemblies
- `message`: Optional - custom message with Metapath templates
- `level`: Typically `INFORMATIONAL` or `WARNING` (not `ERROR`)

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: report
    id: deprecated-element
    level: WARNING
    target: "."
    test: "exists(@deprecated)"
    message: "This element is deprecated. Consider using the replacement."
  - object-type: report
    id: large-collection
    level: INFORMATIONAL
    target: "./items"
    test: "count(./item) > 100"
    message: "Collection has { count(./item) } items, which may impact performance."
```

**XML Example:**
```xml
<constraint>
  <report id="deprecated-element" level="WARNING" target="." test="exists(@deprecated)">
    <message>This element is deprecated. Consider using the replacement.</message>
  </report>
  <report id="large-collection" level="INFORMATIONAL" target="./items" test="count(./item) gt 100">
    <message>Collection has { count(./item) } items, which may impact performance.</message>
  </report>
</constraint>
```

**expect vs report:**
| Constraint | Fires When | Typical Level | Use Case |
|------------|------------|---------------|----------|
| `expect` | test is FALSE | ERROR | Validation failures |
| `report` | test is TRUE | WARNING/INFORMATIONAL | Detected conditions |

### matches

Validates values against datatype and/or regex patterns.

**Attributes:**
- `datatype`: Optional - required datatype format
- `regex`: Optional - pattern match requirement
- `target`: Required for fields/assemblies

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: matches
    target: "./code"
    regex: "^[A-Z]{2}-[0-9]{4}$"
  - object-type: matches
    target: "./email"
    datatype: email-address
```

**XML Example:**
```xml
<constraint>
  <matches target="./code" regex="^[A-Z]{2}-[0-9]{4}$"/>
  <matches target="./email" datatype="email-address"/>
</constraint>
```

### has-cardinality

Enforces occurrence counts for selected nodes. **Assembly only.**

**Attributes:**
- `target`: Required - Metapath expression selecting nodes to count
- `min-occurs`: Optional - minimum required occurrences
- `max-occurs`: Optional - maximum allowed occurrences (or `unbounded`)

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: has-cardinality
    target: "./contact"
    min-occurs: 1
    max-occurs: 5
    message: "Must have between 1 and 5 contacts."
```

**XML Example:**
```xml
<constraint>
  <has-cardinality target="./contact" min-occurs="1" max-occurs="5">
    <message>Must have between 1 and 5 contacts.</message>
  </has-cardinality>
</constraint>
```

### index

Creates a named index of nodes with composite keys. **Assembly only.**

**Attributes:**
- `name`: Required - unique index identifier
- `target`: Required - Metapath expression selecting nodes to index

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: index
    name: user-index
    target: ".//user"
    key-fields:
    - target: "@id"
```

**XML Example:**
```xml
<constraint>
  <index name="user-index" target=".//user">
    <key-field target="@id"/>
  </index>
</constraint>
```

**Composite Keys:**
```yaml
- object-type: index
  name: user-dept-index
  target: ".//user"
  key-fields:
  - target: "@id"
  - target: "@department"
```

### index-has-key

Validates that cross-references exist in a named index.

**Attributes:**
- `name`: Required - references the index to check against
- `target`: Required - Metapath expression selecting nodes to validate

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: index-has-key
    name: user-index
    target: "./user-ref"
    key-fields:
    - target: "@ref"
    message: "Referenced user '{ @ref }' does not exist."
```

**XML Example:**
```xml
<constraint>
  <index-has-key name="user-index" target="./user-ref">
    <key-field target="@ref"/>
    <message>Referenced user '{ @ref }' does not exist.</message>
  </index-has-key>
</constraint>
```

### is-unique

Ensures computed keys are unique (inline uniqueness check). **Assembly only.**

**Attributes:**
- `target`: Required - Metapath expression selecting nodes to check

**YAML Example:**
```yaml
constraint:
  rules:
  - object-type: is-unique
    target: "./entry"
    key-fields:
    - target: "@code"
    message: "Duplicate entry code '{ @code }' found."
```

**XML Example:**
```xml
<constraint>
  <is-unique target="./entry">
    <key-field target="@code"/>
    <message>Duplicate entry code '{ @code }' found.</message>
  </is-unique>
</constraint>
```

### let (Variable Definitions)

Defines reusable variables for subsequent constraint evaluation.

**YAML Example:**
```yaml
constraint:
  lets:
  - var: parent
    expression: ".."
  - var: sibling-count
    expression: "count($parent/sibling)"
  rules:
  - object-type: expect
    target: "."
    test: "$sibling-count >= 1"
    message: "Must have at least one sibling."
```

**XML Example:**
```xml
<constraint>
  <let var="parent" expression=".."/>
  <let var="sibling-count" expression="count($parent/sibling)"/>
  <expect target="." test="$sibling-count ge 1">
    <message>Must have at least one sibling.</message>
  </expect>
</constraint>
```

## Metapath Expressions

Constraints use Metapath expressions based on XPath 3.1. See the **`metapath-expressions` skill** for comprehensive documentation on:

- Path expressions and axis navigation
- Predicates and filtering
- Operators (comparison, logical, arithmetic)
- Function library (string, numeric, sequence, date/time, array, map)
- Variable references
- Common expression patterns

## External Constraints

External constraint modules apply constraints across modules without modifying original definitions.

### metaschema-meta-constraints Format

**XML:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<metaschema-meta-constraints
    xmlns="http://csrc.nist.gov/ns/oscal/metaschema/1.0">

  <import href="other-constraints.xml"/>

  <context>
    <metapath target="/catalog"/>
    <constraints>
      <expect target="./metadata/title" test="string-length(.) > 0">
        <message>Catalog must have a title.</message>
      </expect>
    </constraints>

    <context>
      <metapath target="./group"/>
      <constraints>
        <has-cardinality target="./control" min-occurs="1"/>
      </constraints>
    </context>
  </context>
</metaschema-meta-constraints>
```

### Context Nesting

Constraints can be nested within contexts for scoping:

```xml
<context>
  <metapath target="/document"/>
  <constraints>
    <!-- document-level constraints -->
  </constraints>

  <context>
    <metapath target="./section"/>
    <constraints>
      <!-- section-level constraints -->
    </constraints>
  </context>
</context>
```

### Definition Context

Target specific definitions by name and namespace:

```xml
<definition-context name="user" namespace="http://example.com/ns">
  <constraints>
    <rules>
      <expect target="./email" test="matches(., '.+@.+\..+')"/>
    </rules>
  </constraints>
</definition-context>
```

## Constraint Severity Levels

| Level | Description |
|-------|-------------|
| `CRITICAL` | Serious fault preventing typical use |
| `ERROR` | Fault in content (default) |
| `WARNING` | Potential issue |
| `INFORMATIONAL` | Point of interest |
| `DEBUG` | For model/tool development |

## Best Practices

### When to Use Inline vs External Constraints

**Use Inline Constraints When:**
- Constraint is inherent to the definition's semantics
- Constraint applies universally to all uses
- Simple value restrictions (allowed-values, matches)

**Use External Constraints When:**
- Applying rules across multiple modules
- Profile-specific or context-specific rules
- Constraints that may change independently of the model
- Cross-cutting validation concerns

### Constraint Design Patterns

**Hierarchical Validation:**
```yaml
constraint:
  rules:
  - object-type: expect
    target: "./child"
    test: "count(../child) <= @max-children"
    message: "Too many children (max: { @max-children })."
```

**Cross-Reference Validation:**
```yaml
# First, create the index at the container level
- object-type: index
  name: component-index
  target: ".//component"
  key-fields:
  - target: "@uuid"

# Then validate references
- object-type: index-has-key
  name: component-index
  target: ".//component-ref"
  key-fields:
  - target: "@component-uuid"
```

**Conditional Validation:**
```yaml
constraint:
  rules:
  - object-type: expect
    target: "./start-date"
    test: "not(exists(../end-date)) or . <= ../end-date"
    message: "Start date must be before end date when end date is specified."
```

### Common Anti-Patterns

1. **Over-constraining**: Don't add constraints for theoretical issues
2. **Duplicate constraints**: Check if parent/ancestor already validates
3. **Complex regex**: Prefer datatype validation when possible
4. **Missing messages**: Always provide helpful error messages
5. **Hardcoded values**: Use variables for reusable logic

## Testing Constraints

Create test content that exercises constraints:

- **Valid content** that should pass all constraints
- **Invalid content** that should fail with expected messages
- **Edge cases** for boundary conditions

See `.claude/rules/metaschema-authoring.md` for repository-specific testing commands.

## Common Constraint Patterns

### Required Field with Non-Empty Value

```yaml
constraint:
  rules:
  - object-type: expect
    target: "./title"
    test: "exists(.) and string-length(normalize-space(.)) > 0"
    message: "Title is required and cannot be empty."
```

### Mutually Exclusive Fields

```yaml
constraint:
  rules:
  - object-type: expect
    target: "."
    test: "not(exists(./option-a) and exists(./option-b))"
    message: "Only one of option-a or option-b may be specified."
```

### At Least One Of

```yaml
constraint:
  rules:
  - object-type: expect
    target: "."
    test: "exists(./email) or exists(./phone)"
    message: "At least one contact method (email or phone) is required."
```

### Value From Another Field

```yaml
constraint:
  rules:
  - object-type: expect
    target: "./selection"
    test: ". = ancestor::container/options/option/@value"
    message: "Selection must be one of the defined options."
```

### Date Range Validation

```yaml
constraint:
  rules:
  - object-type: expect
    target: "./end-date"
    test: ". >= ../start-date"
    message: "End date ({ . }) must be on or after start date ({ ../start-date })."
```

## References

- [Metaschema Constraints Specification](https://framework.metaschema.dev/specification/syntax/constraints/)
- [Metapath Expression Language](https://framework.metaschema.dev/specification/syntax/metapath/)
- [XPath 3.1 Specification](https://www.w3.org/TR/xpath-31/)
- [XPath Functions 3.1](https://www.w3.org/TR/xpath-functions-31/)

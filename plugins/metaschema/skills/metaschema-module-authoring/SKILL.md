---
name: metaschema-module-authoring
description: Use when creating or modifying Metaschema modules - guides module structure, definitions, format-specific features, and best practices for both YAML and XML formats
---

# Metaschema Module Authoring

This skill guides the creation and modification of Metaschema modules, which define information models that can be serialized to XML, JSON, or YAML.

**Repository-specific conventions** (file locations, namespaces, testing) are in `.claude/rules/metaschema-authoring.md`.

## Module Authoring Formats

Metaschema modules can be authored in either **XML** or **YAML** format. These formats are semantically equivalent:

- Both produce identical module definitions
- Both enable parsing and serialization of data in **all three formats** (XML, JSON, YAML)
- The choice of authoring format does not limit the data formats the module can handle

For example, a module written in YAML can parse XML documents and serialize to JSON, and vice versa. The authoring format is purely a developer preference.

## Module Structure

### YAML Format

```yaml
---
METASCHEMA:
  schema-name: Human-readable module name
  short-name: module-short-name
  namespace: http://example.com/ns/module
  json-base-uri: http://example.com/ns/module
  schema-version: 1.0.0
  definitions:
  - object-type: assembly
    name: root-assembly
    # ... definition content
```

### XML Format

```xml
<?xml version="1.0" encoding="UTF-8"?>
<METASCHEMA xmlns="http://csrc.nist.gov/ns/oscal/metaschema/1.0">
  <schema-name>Human-readable module name</schema-name>
  <schema-version>1.0.0</schema-version>
  <short-name>module-short-name</short-name>
  <namespace>http://example.com/ns/module</namespace>
  <json-base-uri>http://example.com/ns/module</json-base-uri>

  <define-assembly name="root-assembly">
    <!-- definition content -->
  </define-assembly>
</METASCHEMA>
```

### Required Header Elements

| Element | Description |
|---------|-------------|
| `schema-name` | Human-readable module name for documentation |
| `schema-version` | Semantic version (e.g., 1.0.0) |
| `short-name` | Unique identifier, used in artifact names |
| `namespace` | XML namespace URI for elements |
| `json-base-uri` | URI for JSON Schema `$schema` keyword |

### Optional Header Elements

| Element | Description |
|---------|-------------|
| `@abstract` | Set to `yes` for import-only modules (default: `no`) |
| `remarks` | Additional documentation |
| `import` | Import definitions from other modules |

## Definition Types

### Assembly Definitions

Assemblies are structured containers with optional flags and a model (nested content).

**YAML:**
```yaml
- object-type: assembly
  name: user
  formal-name: User
  description: A system user account.
  root-name:
    name: user
  flags:
  - object-type: flag
    name: id
    formal-name: User ID
    description: Unique identifier for the user.
    as-type: uuid
    required: "yes"
  model:
    instances:
    - object-type: field-ref
      ref: username
      min-occurs: 1
    - object-type: assembly-ref
      ref: role
      max-occurs: unbounded
      group-as:
        name: roles
        in-json: ARRAY
```

**XML:**
```xml
<define-assembly name="user">
  <formal-name>User</formal-name>
  <description>A system user account.</description>
  <root-name>user</root-name>
  <define-flag name="id" as-type="uuid" required="yes">
    <formal-name>User ID</formal-name>
    <description>Unique identifier for the user.</description>
  </define-flag>
  <model>
    <field ref="username" min-occurs="1"/>
    <assembly ref="role" max-occurs="unbounded">
      <group-as name="roles" in-json="ARRAY"/>
    </assembly>
  </model>
</define-assembly>
```

### Field Definitions

Fields contain typed values and optional flags.

**YAML:**
```yaml
- object-type: field
  name: email
  formal-name: Email Address
  description: User's email address.
  as-type: email-address
```

**XML:**
```xml
<define-field name="email" as-type="email-address">
  <formal-name>Email Address</formal-name>
  <description>User's email address.</description>
</define-field>
```

### Flag Definitions

Flags are leaf nodes representing simple typed values (attributes in XML, properties in JSON).

**YAML:**
```yaml
- object-type: flag
  name: status
  formal-name: Status
  description: The current status.
  as-type: token
  default: active
```

**XML:**
```xml
<define-flag name="status" as-type="token" default="active">
  <formal-name>Status</formal-name>
  <description>The current status.</description>
</define-flag>
```

## Data Types

See the [Metaschema Data Types specification](https://framework.metaschema.dev/specification/datatypes/) for complete details.

### Simple Data Types (for flags and fields)

| Type | Description |
|------|-------------|
| [`string`](https://framework.metaschema.dev/specification/datatypes/#string) | Unicode string (default for flags) |
| [`token`](https://framework.metaschema.dev/specification/datatypes/#token) | Whitespace-normalized string |
| [`boolean`](https://framework.metaschema.dev/specification/datatypes/#boolean) | `true` or `false` |
| [`integer`](https://framework.metaschema.dev/specification/datatypes/#integer) | Signed integer |
| [`non-negative-integer`](https://framework.metaschema.dev/specification/datatypes/#non-negative-integer) | Zero or positive integer |
| [`positive-integer`](https://framework.metaschema.dev/specification/datatypes/#positive-integer) | Positive integer (> 0) |
| [`decimal`](https://framework.metaschema.dev/specification/datatypes/#decimal) | Decimal number |
| [`uuid`](https://framework.metaschema.dev/specification/datatypes/#uuid) | UUID v4 or v5 |
| [`uri`](https://framework.metaschema.dev/specification/datatypes/#uri) | Absolute URI |
| [`uri-reference`](https://framework.metaschema.dev/specification/datatypes/#uri-reference) | Absolute or relative URI |
| [`date`](https://framework.metaschema.dev/specification/datatypes/#date) | ISO 8601 date |
| [`date-time`](https://framework.metaschema.dev/specification/datatypes/#date-time) | ISO 8601 date-time |
| [`date-with-timezone`](https://framework.metaschema.dev/specification/datatypes/#date-with-timezone) | Date with required timezone |
| [`date-time-with-timezone`](https://framework.metaschema.dev/specification/datatypes/#date-time-with-timezone) | Date-time with required timezone |
| [`email-address`](https://framework.metaschema.dev/specification/datatypes/#email-address) | RFC 5322 email |
| [`hostname`](https://framework.metaschema.dev/specification/datatypes/#hostname) | Domain name |
| [`ip-v4-address`](https://framework.metaschema.dev/specification/datatypes/#ip-v4-address) | IPv4 address |
| [`ip-v6-address`](https://framework.metaschema.dev/specification/datatypes/#ip-v6-address) | IPv6 address |
| [`base64`](https://framework.metaschema.dev/specification/datatypes/#base64) | Base64-encoded binary |

### Markup Data Types (for fields only)

| Type | Description |
|------|-------------|
| [`markup-line`](https://framework.metaschema.dev/specification/datatypes/#markup-line) | Inline markup (no block elements) |
| [`markup-multiline`](https://framework.metaschema.dev/specification/datatypes/#markup-multiline) | Block-level markup content |

## Instance References

### Referencing Definitions

Use `ref` to reference global definitions. All three definition types can be referenced:

- **Assembly references** - in model content
- **Field references** - in model content
- **Flag references** - in assembly or field definitions

**YAML:**
```yaml
# Flag reference (in assembly/field definition)
flags:
- object-type: flag-ref
  ref: id
  required: "yes"

# Assembly and field references (in model)
model:
  instances:
  - object-type: assembly-ref
    ref: address
    min-occurs: 0
    max-occurs: 1
  - object-type: field-ref
    ref: description
```

**XML:**
```xml
<!-- Flag reference -->
<flag ref="id" required="yes"/>

<!-- Assembly and field references in model -->
<model>
  <assembly ref="address" min-occurs="0" max-occurs="1"/>
  <field ref="description"/>
</model>
```

### Inline Definitions

Define and instantiate in one place (for single-use elements):

**YAML:**
```yaml
model:
  instances:
  - object-type: assembly
    name: metadata
    formal-name: Metadata
    description: Document metadata.
    model:
      instances:
      - object-type: field
        name: title
        formal-name: Title
        as-type: string
```

**XML:**
```xml
<model>
  <define-assembly name="metadata">
    <formal-name>Metadata</formal-name>
    <description>Document metadata.</description>
    <model>
      <define-field name="title" as-type="string">
        <formal-name>Title</formal-name>
      </define-field>
    </model>
  </define-assembly>
</model>
```

### Code Generation: Nested vs Separate Classes

The choice between inline definitions and global definitions with references affects how Java classes are generated:

| Approach | Generated Structure | Use When |
|----------|---------------------|----------|
| Inline definitions | Nested inner classes | Single-use, tightly coupled structures |
| Global definitions + refs | Separate top-level classes | Reusable, shared across multiple parents |

#### Example: Inline produces nested classes

```yaml
# This YAML structure with inline definitions:
- object-type: assembly
  name: parent
  model:
    instances:
    - object-type: assembly
      name: child
      formal-name: Child
      model:
        instances:
        - object-type: field
          name: value
          as-type: string
```

Generates:
```java
public class Parent {
    public static class Child {
        // nested inside Parent
    }
}
```

#### Example: References produce separate classes

```yaml
# This YAML structure with global definitions and refs:
definitions:
- object-type: assembly
  name: parent
  model:
    instances:
    - object-type: assembly-ref
      ref: child

- object-type: assembly
  name: child
  formal-name: Child
```

Generates:
```java
public class Parent { ... }
public class Child { ... }  // separate file
```

**Key insight**: Use inline definitions when you want tightly coupled, nested class hierarchies (like binding configurations with deeply nested elements). Use global definitions with refs when the component is reused elsewhere.

## Cardinality and Grouping

### Cardinality Attributes

| Attribute | Default | Description |
|-----------|---------|-------------|
| `min-occurs` | 0 | Minimum occurrences |
| `max-occurs` | 1 | Maximum occurrences (use `unbounded` for unlimited) |

### group-as Element

Required when `max-occurs` > 1. Controls collection representation:

**YAML:**
```yaml
- object-type: assembly-ref
  ref: item
  max-occurs: unbounded
  group-as:
    name: items
    in-json: ARRAY
    in-xml: UNGROUPED
```

**XML:**
```xml
<assembly ref="item" max-occurs="unbounded">
  <group-as name="items" in-json="ARRAY" in-xml="UNGROUPED"/>
</assembly>
```

## XML-Specific Features

### in-xml Attribute (for fields)

| Value | Description |
|-------|-------------|
| `WRAPPED` | Field wrapped in element (default) |
| `UNWRAPPED` | Field content appears directly (markup-multiline only) |

### in-xml Attribute (for group-as)

| Value | Description |
|-------|-------------|
| `UNGROUPED` | No wrapper element (default) |
| `GROUPED` | Items wrapped in container element |

### Root Element

Use `root-name` on an assembly to designate it as a document root:

```yaml
- object-type: assembly
  name: catalog
  root-name:
    name: catalog
```

## JSON/YAML-Specific Features

### in-json Attribute (for group-as)

| Value | Description |
|-------|-------------|
| `ARRAY` | Always use array notation |
| `SINGLETON_OR_ARRAY` | Single value or array (default) |
| `BY_KEY` | Object with flag values as property names |

### json-key Element

Used with `in-json="BY_KEY"` to group items as object properties instead of an array. The referenced flag's value becomes the property name.

**YAML:**
```yaml
- object-type: assembly
  name: parameter
  flags:
  - object-type: flag
    name: id
    as-type: token
    required: "yes"
  json-key:
    flag-ref: id
  # When used with group-as in-json: BY_KEY, produces:
  # "parameters": { "param1": {...}, "param2": {...} }
  # instead of: "parameters": [{...}, {...}]
```

**XML:**
```xml
<define-assembly name="parameter">
  <json-key flag-ref="id"/>
  <define-flag name="id" as-type="token" required="yes"/>
</define-assembly>
```

### json-value-key and json-value-key-flag Elements

Control the property name for a field's value in JSON. Two forms are available:

#### Literal Value (json-value-key)

Use a static property name for the field's value:

**YAML:**
```yaml
- object-type: field
  name: title
  as-type: string
  json-value-key: text
  # Produces: { "text": "the value" }
```

**XML:**
```xml
<define-field name="title" as-type="string">
  <json-value-key>text</json-value-key>
</define-field>
```

#### Flag-Based Value (json-value-key-flag)

Use a flag's value as the property name for the field's value:

**YAML:**
```yaml
- object-type: field
  name: property
  as-type: string
  flags:
  - object-type: flag
    name: name
    as-type: token
    required: "yes"
  json-value-key-flag:
    flag-ref: name
  # If flag value is "color", produces: { "color": "blue" }
```

**XML:**
```xml
<define-field name="property" as-type="string">
  <json-value-key-flag flag-ref="name"/>
  <define-flag name="name" as-type="token" required="yes"/>
</define-field>
```

## Module Imports

Import definitions from other modules. **Imports are cross-format compatible**: a YAML module can import XML modules, and an XML module can import YAML modules. The authoring format of the imported module does not affect compatibility.

**YAML importing both formats:**
```yaml
METASCHEMA:
  # ... header elements
  imports:
  - href: ../common/common-types.yaml    # YAML module
  - href: ../legacy/old-definitions.xml  # XML module works too
  definitions:
  # ... use imported definitions from either format
```

**XML importing both formats:**
```xml
<METASCHEMA>
  <!-- header elements -->
  <import href="../common/common-types.xml"/>   <!-- XML module -->
  <import href="../new/new-definitions.yaml"/>  <!-- YAML module works too -->
  <!-- definitions -->
</METASCHEMA>
```

## Scope Control

The `scope` attribute controls definition visibility across module imports.

| Value | Description |
|-------|-------------|
| `global` | Available to importing modules (default) |
| `local` | Only available within the defining module |

### Definition Lookup Rules

When a definition is referenced (via `ref`), the lookup follows this order:

1. **Current module first**: Check all top-level definitions in the current module (both `global` and `local`)
2. **Imported modules second**: Check exported (`global`) definitions from imported modules

### Export and Import Behavior

- **`global` definitions** are exported to importing modules
- **`local` definitions** are never exported; they can only be referenced within their defining module
- **Root assemblies** (those with `root-name`) are always exported regardless of scope

### Name Resolution with Imports

When multiple imported modules define the same name:

1. Only `global` scoped definitions are considered for import
2. If names conflict, the **most recently imported** definition wins
3. Definitions in the **importing module always override** imported definitions
4. Each module resolves references using the resulting combined set

**YAML:**
```yaml
# Module A - defines a local helper
- object-type: field
  name: internal-helper
  scope: local           # Not exported
  as-type: string

# Module A - defines a global type
- object-type: field
  name: shared-type
  scope: global          # Exported (this is the default)
  as-type: token
```

**XML:**
```xml
<!-- Local definition - only usable within this module -->
<define-field name="internal-helper" scope="local" as-type="string"/>

<!-- Global definition - available to importing modules -->
<define-field name="shared-type" scope="global" as-type="token"/>
```

### Use Cases

- **`local`**: Internal helper definitions, implementation details not meant for external use
- **`global`**: Reusable definitions intended to be shared across modules

## choice Element

For mutually exclusive content:

**XML:**
```xml
<model>
  <choice>
    <field ref="inline-content"/>
    <assembly ref="external-reference"/>
  </choice>
</model>
```

## Best Practices

1. **Use semantic naming**: Names should describe the data's meaning, not its format
2. **Document everything**: Always include `formal-name` and `description`
3. **Prefer global definitions**: Reuse definitions across the module
4. **Use appropriate data types**: Choose the most specific type for validation
5. **Consider both formats**: Think about XML and JSON representation when designing
6. **Version your modules**: Use semantic versioning in `schema-version`
7. **Keep modules focused**: Split large modules using imports

## Common Patterns

### Identifier Flag

```yaml
- object-type: flag
  name: uuid
  formal-name: Universally Unique Identifier
  description: A unique identifier for this element.
  as-type: uuid
  required: "yes"
```

### Location Reference

```yaml
- object-type: flag
  name: location
  formal-name: Location
  description: A URI reference to the resource location.
  as-type: uri-reference
  required: "yes"
```

### Enumerated Token with Constraint

```yaml
- object-type: flag
  name: format
  formal-name: Format
  description: The content format.
  as-type: token
  constraint:
    rules:
    - object-type: allowed-values
      enums:
      - value: XML
        remark: XML format.
      - value: JSON
        remark: JSON format.
      - value: YAML
        remark: YAML format.
```

### Collection with Grouping

```yaml
model:
  instances:
  - object-type: assembly-ref
    ref: item
    min-occurs: 1
    max-occurs: unbounded
    group-as:
      name: items
      in-json: ARRAY
```

### Optional Field

```yaml
model:
  instances:
  - object-type: field-ref
    ref: description
    min-occurs: 0
    max-occurs: 1
```

## References

- [Metaschema Specification](https://framework.metaschema.dev/specification/)
- [Metaschema Data Types](https://framework.metaschema.dev/specification/datatypes/)
- [Format Bindings](https://framework.metaschema.dev/specification/mapping/)

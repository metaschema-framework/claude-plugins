---
name: oscal-mapping
description: Use when creating, understanding, or working with OSCAL Mappings that define relationships between controls in different frameworks
---

# OSCAL Mapping Model

> **Status: Experimental**
>
> The Mapping model is less mature than other OSCAL models. There is a known bug in oscal-cli
> that prevents full validation of mapping documents. The schema structure documented here
> is based on direct analysis of the OSCAL JSON schema and liboscal-java implementation.
> Use with caution and expect potential changes in future OSCAL releases.

The Mapping model defines relationships between controls in different catalogs, enabling crosswalks between security frameworks and supporting gap analysis. Based on [NIST IR 8477](https://doi.org/10.6028/NIST.IR.8477) set theory relationship mapping.

**Related skills:**
- `oscal-catalog` - Catalogs being mapped between
- `oscal-profile` - Baselines that may use mapped controls

## Purpose

Control mappings:
- **Define relationships** between controls in different frameworks
- **Enable crosswalks** (e.g., NIST 800-53 to ISO 27001)
- **Support gap analysis** for multi-framework compliance
- **Document equivalence** and coverage between standards
- **Facilitate compliance** with multiple frameworks

## Document Structure

```yaml
mapping-collection:
  uuid: <unique-identifier>
  metadata:
    title: "Control Framework Mapping"
    version: "1.0.0"
    oscal-version: "1.2.0"
  provenance:                              # REQUIRED
    method: human                          # human|automation|hybrid
    matching-rationale: semantic           # syntactic|semantic|functional
    status: complete                       # complete|not-complete|draft|deprecated|superseded
    mapping-description: >-               # REQUIRED - describes the mapping purpose
      Description of this mapping's scope and intended use.
  mappings:
    - uuid: mapping-uuid
      source-resource:
        type: catalog                      # REQUIRED: catalog|profile
        href: "source-catalog.json"        # REQUIRED
      target-resource:
        type: catalog                      # REQUIRED: catalog|profile
        href: "target-catalog.json"        # REQUIRED
      maps: []
  back-matter:
    resources: []
```

## Provenance (Required)

Every mapping collection requires provenance information with **four required fields**:

```yaml
provenance:
  method: human                  # How the mapping was created (REQUIRED)
  matching-rationale: semantic   # How relationships were determined (REQUIRED)
  status: complete               # Current status of the mapping (REQUIRED)
  mapping-description: >-        # REQUIRED - context and intended use
    This mapping establishes relationships between Framework A and Framework B.
    Use for compliance gap analysis and framework migration.
```

### Method Values

| Value | Description |
|-------|-------------|
| `human` | Mapping created by human analysis |
| `automation` | Mapping created by automated tools |
| `hybrid` | Combination of human and automated analysis |

### Matching Rationale Values

| Value | Description |
|-------|-------------|
| `syntactic` | Word-for-word analysis of wording similarity |
| `semantic` | Analysis of meaning similarity (requires interpretation) |
| `functional` | Analysis of execution results similarity |

### Status Values

| Value | Description |
|-------|-------------|
| `complete` | Mapping is finished |
| `not-complete` | Mapping is in progress |
| `draft` | Preliminary mapping |
| `deprecated` | No longer recommended |
| `superseded` | Replaced by newer mapping |

## Resource References

Source and target resources require `type` and `href` fields. The `title` field is NOT supported:

```yaml
mappings:
  - uuid: "mapping-uuid"
    source-resource:
      type: catalog              # REQUIRED: catalog or profile
      href: "./nist-800-53.json" # REQUIRED
    target-resource:
      type: catalog              # REQUIRED: catalog or profile
      href: "./iso-27001.json"   # REQUIRED
    maps: [...]
```

| Field | Required | Description |
|-------|----------|-------------|
| `type` | Yes | Type of resource: `catalog` or `profile` |
| `href` | Yes | URI reference to the resource |

## Maps Structure

Each map defines a relationship between source and target controls:

```yaml
maps:
  - uuid: map-uuid
    relationship: equivalent-to        # REQUIRED: Relationship at map level
    sources:                           # REQUIRED: List of source items
      - type: control
        id-ref: ac-1
    targets:                           # REQUIRED: List of target items (min 1)
      - type: control
        id-ref: A.5.1.1
    confidence-score:                  # Optional: Complex object
      category: high                   # OR percentage: 0.95
    remarks: "Both controls address access policy requirements"
```

## Relationship Types

Relationships are defined at the `map` level (not nested under targets):

| Type | Description | Example |
|------|-------------|---------|
| `equivalent-to` | Similar meaning, same effective requirements | AC-1 ~ A.5.1.1 |
| `equal-to` | Identical requirements (rare between frameworks) | Exact match |
| `subset-of` | Source is covered by broader target | Source < Target |
| `superset-of` | Source covers more than target | Source > Target |
| `intersects-with` | Partial overlap between requirements | Some overlap |
| `no-relationship` | No meaningful relationship | Gap identification |

### Using Relationships

```yaml
maps:
  # Equivalent relationship
  - uuid: map-1-uuid
    relationship: equivalent-to
    sources:
      - type: control
        id-ref: ac-1
    targets:
      - type: control
        id-ref: A.5.1.1
    remarks: "Both require access control policy documentation"

  # Subset relationship (source is narrower)
  - uuid: map-2-uuid
    relationship: subset-of
    sources:
      - type: control
        id-ref: ac-2
    targets:
      - type: control
        id-ref: A.9.2.1
    remarks: "AC-2 account management is subset of ISO user access"
```

## Source and Target Types

Both sources and targets reference controls or statements:

```yaml
# Control reference
sources:
  - type: control
    id-ref: ac-2

# Statement reference (more granular)
sources:
  - type: statement
    id-ref: ac-2_smt
```

| Type | Description |
|------|-------------|
| `control` | Full control reference |
| `statement` | Control statement reference |

## Multiple Sources and Targets

A map can relate multiple sources to multiple targets:

```yaml
maps:
  - uuid: multi-mapping-uuid
    relationship: intersects-with
    sources:
      - type: control
        id-ref: ia-2
      - type: control
        id-ref: ia-5
    targets:
      - type: control
        id-ref: A.9.2.1
      - type: control
        id-ref: A.9.4.2
      - type: control
        id-ref: A.9.4.3
    remarks: "NIST authentication controls map to multiple ISO controls"
```

## Confidence Score

The `confidence-score` is a **complex object** with either a `category` flag or a `percentage` flag (not both):

```yaml
# Using category (predefined values)
confidence-score:
  category: high    # high, medium, or low

# Using percentage (0.0 to 1.0)
confidence-score:
  percentage: 0.95  # 95% confidence
```

| Field | Type | Description |
|-------|------|-------------|
| `category` | string | Predefined category: `high`, `medium`, `low` |
| `percentage` | decimal | Numeric value between 0.0 and 1.0 |

**Important:** Use either `category` OR `percentage`, not both.

## Properties on Maps

Add metadata properties to maps:

```yaml
maps:
  - uuid: map-uuid
    relationship: equivalent-to
    sources:
      - type: control
        id-ref: ac-1
    targets:
      - type: control
        id-ref: A.5.1.1
    props:
      - name: reviewed-by
        value: "security-team"
      - name: review-date
        value: "2024-01-15"
    remarks: "Reviewed and approved by security team"
```

## Gap Analysis

Identify gaps using `no-relationship`. **Important:** The `targets` array must have at least 1 item - reference a related but non-equivalent control for context:

```yaml
maps:
  # Gap - source has no direct equivalent in target framework
  - uuid: gap-uuid
    relationship: no-relationship
    sources:
      - type: control
        id-ref: ac-22
    targets:
      - type: control
        id-ref: A.8.2.1           # Related but non-equivalent control
    confidence-score:
      category: high
    remarks: >-
      COVERAGE GAP: AC-22 (Publicly Accessible Content) has no direct
      equivalent in ISO 27001. A.8.2.1 addresses information classification
      but from an asset management perspective rather than public content.
```

## Complete Example

```yaml
mapping-collection:
  uuid: 11111111-aaaa-4bbb-8ccc-dddddddddddd
  metadata:
    title: "NIST 800-53 to ISO 27001 Mapping"
    last-modified: "2024-01-15T12:00:00Z"
    version: "1.0.0"
    oscal-version: "1.2.0"
    remarks: "Framework crosswalk for compliance mapping"

  provenance:
    method: human
    matching-rationale: semantic
    status: complete
    mapping-description: >-
      This mapping establishes semantic relationships between NIST SP 800-53
      and ISO/IEC 27001. Use for compliance gap analysis and multi-framework
      compliance demonstrations.

  mappings:
    - uuid: 22222222-bbbb-4ccc-8ddd-eeeeeeeeeeee
      source-resource:
        type: catalog
        href: "./nist-800-53-catalog.json"
      target-resource:
        type: catalog
        href: "./iso-27001-catalog.json"
      maps:
        - uuid: 33333333-cccc-4ddd-8eee-ffffffffffff
          relationship: equivalent-to
          sources:
            - type: control
              id-ref: ac-1
          targets:
            - type: control
              id-ref: A.5.1.1
          confidence-score:
            category: high
          remarks: "Both require documented access control policy"

        - uuid: 44444444-dddd-4eee-8fff-000000000000
          relationship: subset-of
          sources:
            - type: control
              id-ref: ac-2
          targets:
            - type: control
              id-ref: A.9.2.1
          confidence-score:
            percentage: 0.85
          remarks: "AC-2 is narrower than ISO user registration"

  back-matter:
    resources:
      - uuid: 55555555-eeee-4fff-8000-111111111111
        title: "Mapping Methodology"
        remarks: "Semantic analysis based on NIST IR 8477"
```

## Best Practices

1. **Set full provenance**: Include method, rationale, status, AND mapping-description
2. **Include resource type**: Always specify `type: catalog` or `type: profile`
3. **Use precise relationships**: Choose the most accurate relationship type
4. **Document rationale**: Explain why controls are related in remarks
5. **Include confidence**: Use confidence-score with category or percentage
6. **Consider direction**: Mapping A to B may differ from B to A
7. **Note gaps**: Use `no-relationship` with a related control as target for context
8. **Validate**: Have subject matter experts review mappings

## Known Issues

> **oscal-cli Validation Bug**
>
> As of oscal-cli 3.0.0, there is a bug in the `has-oscal-namespace` constraint that
> causes validation failures for the `relationship` field. The error is:
> ```
> InvalidTypeMetapathException: Expected type 'IAssemblyNodeItem',
> but the node was type 'FieldInstanceNodeItemImpl'
> ```
> This is a bug in the validator, not in your mapping file. The mapping structure
> documented here is correct according to the OSCAL JSON schema.

## Validation

```bash
# Validate mapping collection (may show constraint error due to known bug)
oscal-cli validate mapping-collection.yaml
```

## Local Examples

Annotated sample mapping in this repository:

| Format | File | Notes |
|--------|------|-------|
| YAML | `examples/mapping/sample-mapping.yaml` | Comprehensive example with comments |
| YAML | `examples/mapping/source-catalog.yaml` | Source framework (Framework A) |
| YAML | `examples/mapping/target-catalog.yaml` | Target framework (Framework B) |
| XML | `examples/mapping/source-catalog.xml` | Annotated source catalog |
| XML | `examples/mapping/target-catalog.xml` | Annotated target catalog |

The mapping example demonstrates:
- All relationship types (equivalent-to, subset-of, superset-of, intersects-with)
- Multiple sources and targets
- Confidence scores (both category and percentage styles)
- Gap identification with no-relationship
- Required provenance with mapping-description

## Official Examples

| Example | Location |
|---------|----------|
| NIST Crosswalks | [CPRT Catalog](https://csrc.nist.gov/Projects/cprt/catalog#/cprt) |
| SP 800-53 to CSF Mapping | Available via NIST CPRT tool |
| NIST IR 8477 | [Set Theory Relationship Mapping](https://doi.org/10.6028/NIST.IR.8477) |

## Resources

- [OSCAL Mapping Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/control/mapping/)
- [NIST CPRT Tool](https://csrc.nist.gov/Projects/cprt/catalog#/cprt)
- [NIST IR 8477 - Mapping Relationships](https://doi.org/10.6028/NIST.IR.8477)

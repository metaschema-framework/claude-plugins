---
name: oscal-mapping
description: Use when creating, understanding, or working with OSCAL Mappings that define relationships between controls in different frameworks
---

# OSCAL Mapping Model

The Mapping model defines relationships between controls in different catalogs, enabling crosswalks between security frameworks and supporting gap analysis.

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
    oscal-version: "1.1.2"
  mappings:
    - uuid: mapping-uuid
      source-resource:
        href: "source-catalog.json"
      target-resource:
        href: "target-catalog.json"
      maps: []
  back-matter:
    resources: []
```

## Mappings

Each mapping connects a source catalog to a target:

```yaml
mappings:
  - uuid: "mapping-uuid"
    source-resource:
      href: "https://example.com/nist-800-53-catalog.json"
      title: "NIST SP 800-53"
    target-resource:
      href: "https://example.com/iso-27001-catalog.json"
      title: "ISO 27001"
    props:
      - name: mapping-type
        value: "framework-crosswalk"
    maps:
      - uuid: map-uuid
        source:
          type: control
          id-ref: ac-1
        targets:
          - type: control
            id-ref: A.5.1.1
            relationships:
              - type: equivalent
                remarks: "ISO control provides equivalent coverage"
```

## Relationship Types

| Type | Description | Example |
|------|-------------|---------|
| `equivalent` | Controls are functionally equivalent | AC-1 ≈ A.5.1.1 |
| `equal` | Controls are identical | Rare between frameworks |
| `subset` | Source is subset of target | Source covered by broader target |
| `superset` | Source is superset of target | Source covers more than target |
| `intersects` | Partial overlap | Some but not all requirements match |

### Using Relationships

```yaml
maps:
  - uuid: map-1-uuid
    source:
      type: control
      id-ref: ac-2
    targets:
      - type: control
        id-ref: A.9.2.1
        relationships:
          - type: subset
            remarks: "AC-2 account management is subset of ISO user access"
      - type: control
        id-ref: A.9.2.2
        relationships:
          - type: intersects
            remarks: "Partial overlap with user access provisioning"
```

## Source and Target Types

Both source and target can reference different levels:

```yaml
source:
  type: control           # Full control
  id-ref: ac-2

source:
  type: statement         # Control statement
  id-ref: ac-2_smt.a

source:
  type: part              # Control part
  id-ref: ac-2_gdn
```

## Multiple Targets

A single source can map to multiple targets:

```yaml
maps:
  - uuid: multi-target-uuid
    source:
      type: control
      id-ref: ia-2
    targets:
      - type: control
        id-ref: A.9.2.1
        relationships:
          - type: intersects
      - type: control
        id-ref: A.9.4.2
        relationships:
          - type: intersects
      - type: control
        id-ref: A.9.4.3
        relationships:
          - type: intersects
    remarks: "NIST IA-2 maps to multiple ISO 27001 controls"
```

## Common Framework Mappings

### NIST 800-53 to ISO 27001

```yaml
mappings:
  - uuid: nist-to-iso-uuid
    source-resource:
      href: "nist-800-53-catalog.json"
    target-resource:
      href: "iso-27001-catalog.json"
    maps:
      - uuid: ac-mapping-uuid
        source:
          type: control
          id-ref: ac-1
        targets:
          - type: control
            id-ref: A.5.1.1
            relationships:
              - type: equivalent
          - type: control
            id-ref: A.5.1.2
            relationships:
              - type: intersects
```

### NIST 800-53 to NIST CSF

```yaml
mappings:
  - uuid: nist-to-csf-uuid
    source-resource:
      href: "nist-800-53-catalog.json"
    target-resource:
      href: "nist-csf-catalog.json"
    maps:
      - uuid: csf-mapping-uuid
        source:
          type: control
          id-ref: ac-2
        targets:
          - type: control
            id-ref: PR.AC-1
            relationships:
              - type: equivalent
```

## Gap Analysis

Use mappings to identify gaps:

```yaml
maps:
  # Mapped controls
  - uuid: mapped-uuid
    source:
      type: control
      id-ref: ac-1
    targets:
      - type: control
        id-ref: A.5.1.1
        relationships:
          - type: equivalent

  # Gap - no equivalent target
  - uuid: gap-uuid
    source:
      type: control
      id-ref: ac-22
    props:
      - name: coverage
        value: "gap"
    remarks: "No ISO 27001 equivalent for publicly accessible content"
```

## Bidirectional Mappings

Create mappings in both directions:

```yaml
mappings:
  # NIST to ISO
  - uuid: nist-to-iso-uuid
    source-resource:
      href: "nist-800-53.json"
    target-resource:
      href: "iso-27001.json"
    maps: [...]

  # ISO to NIST
  - uuid: iso-to-nist-uuid
    source-resource:
      href: "iso-27001.json"
    target-resource:
      href: "nist-800-53.json"
    maps: [...]
```

## Mapping Metadata

Document mapping provenance:

```yaml
metadata:
  title: "NIST 800-53 to ISO 27001 Mapping"
  version: "1.0.0"
  oscal-version: "1.1.2"
  props:
    - name: mapping-version
      value: "2024-01"
    - name: source-version
      value: "NIST SP 800-53 Rev 5"
    - name: target-version
      value: "ISO/IEC 27001:2022"
  responsible-parties:
    - role-id: mapping-author
      party-uuids:
        - author-uuid
  remarks: "Official crosswalk maintained by security team"
```

## Properties

Add metadata to maps:

```yaml
maps:
  - uuid: map-uuid
    props:
      - name: confidence
        value: "high"
      - name: rationale
        value: "Both controls address access policy requirements"
      - name: review-status
        value: "approved"
      - name: effective-date
        value: "2024-01-01"
    source:
      type: control
      id-ref: ac-1
    targets:
      - type: control
        id-ref: A.5.1.1
        props:
          - name: coverage-level
            value: "full"
        relationships:
          - type: equivalent
```

## Best Practices

1. **Document rationale**: Explain why controls are related
2. **Use precise relationship types**: Choose the most accurate type
3. **Include version information**: Track framework versions
4. **Review regularly**: Update mappings when frameworks change
5. **Consider direction**: Mapping A→B may differ from B→A
6. **Note partial coverage**: Use remarks for nuanced relationships
7. **Validate mappings**: Have subject matter experts review

## Use Cases

1. **Multi-framework compliance**: Map one implementation to multiple standards
2. **Gap analysis**: Identify controls without equivalents
3. **Migration**: Move from one framework to another
4. **Reciprocity**: Demonstrate equivalent security posture
5. **Audit support**: Show control coverage across standards

## Validation

```bash
# Validate mapping collection
oscal-cli validate mapping.json
```

## Official Examples

| Example | Location |
|---------|----------|
| NIST Crosswalks | [CPRT Catalog](https://csrc.nist.gov/Projects/cprt/catalog#/cprt) |
| SP 800-53 to CSF Mapping | Available via NIST CPRT tool |

Note: OSCAL mapping examples are less common as this is a newer model. Use the NIST Cybersecurity and Privacy Reference Tool (CPRT) for official crosswalks.

## Resources

- [OSCAL Mapping Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/control/mapping/)
- [NIST CPRT Tool](https://csrc.nist.gov/Projects/cprt/catalog#/cprt)

---
name: oscal-catalog
description: Use when creating, understanding, or working with OSCAL catalog documents that define security controls
---

# OSCAL Catalog Model

The Catalog model is the foundation of OSCAL. It defines security controls that can be selected and tailored by profiles, implemented in SSPs, and assessed.

**Related skills:**
- `oscal-basics` - OSCAL overview and concepts
- `oscal-profile` - Selecting and tailoring controls from catalogs

## Purpose

A catalog organizes a collection of security controls into groups and provides detailed control definitions including:
- Control statements (requirements)
- Parameters (configurable values)
- Control objectives
- Assessment methods
- Guidance and references

## Common Catalogs

| Catalog | Description |
|---------|-------------|
| NIST SP 800-53 | Federal information systems security controls |
| NIST SP 800-171 | Protecting CUI in non-federal systems |
| ISO 27001 | International information security standard |
| FedRAMP | Federal cloud service provider requirements |
| CISA SCuBA | Secure Cloud Business Applications |

## Document Structure

```yaml
catalog:
  uuid: <unique-identifier>
  metadata:
    title: "Catalog Title"
    version: "1.0.0"
    oscal-version: "1.1.2"
  groups:           # Optional hierarchical organization
    - id: ac
      title: "Access Control"
      controls:
        - id: ac-1
          title: "Policy and Procedures"
  controls:         # Top-level controls (if not in groups)
    - id: control-1
      title: "Control Title"
  back-matter:      # References and resources
    resources: []
```

## Control Structure

### Control Definition

```yaml
controls:
  - id: ac-1
    class: SP800-53
    title: "Policy and Procedures"
    params:
      - id: ac-1_prm_1
        label: "organization-defined personnel or roles"
    props:
      - name: label
        value: "AC-1"
      - name: sort-id
        value: "ac-01"
    parts:
      - id: ac-1_smt
        name: statement
        parts:
          - id: ac-1_smt.a
            name: item
            props:
              - name: label
                value: "a."
            prose: "Develop, document, and disseminate..."
      - id: ac-1_gdn
        name: guidance
        prose: "Access control policy..."
    controls:  # Control enhancements (nested controls)
      - id: ac-1.1
        title: "Enhancement One"
```

### Parameters

Parameters allow controls to be customized:

```yaml
params:
  - id: ac-1_prm_1
    label: "organization-defined personnel or roles"
    select:
      how-many: one-or-more
      choice:
        - security officer
        - system administrator
    guidelines:
      - prose: "Select appropriate personnel..."
```

### Parts

Parts define control components:

| Part Name | Purpose |
|-----------|---------|
| `statement` | The control requirement text |
| `guidance` | Implementation guidance |
| `objective` | Assessment objectives |
| `assessment` | Assessment methods |
| `item` | Numbered/lettered sub-items |

## Groups

Groups organize controls hierarchically:

```yaml
groups:
  - id: ac
    title: "Access Control"
    class: family
    props:
      - name: label
        value: "AC"
    groups:  # Nested groups
      - id: ac-access
        title: "Access Management"
    controls:
      - id: ac-1
        title: "Policy and Procedures"
```

## Properties and Links

### Properties

```yaml
props:
  - name: label
    value: "AC-1"
  - name: sort-id
    value: "ac-01"
  - name: status
    value: "withdrawn"
    remarks: "Incorporated into AC-2"
```

### Links

```yaml
links:
  - href: "#resource-uuid"
    rel: reference
    text: "NIST SP 800-63"
  - href: "#ac-2"
    rel: related
```

## Back Matter

Resources referenced by controls:

```yaml
back-matter:
  resources:
    - uuid: resource-uuid
      title: "NIST SP 800-63"
      citation:
        text: "NIST Special Publication 800-63"
      rlinks:
        - href: "https://doi.org/10.6028/NIST.SP.800-63-3"
```

## Best Practices

1. **Use consistent IDs**: Follow a naming convention (e.g., `ac-1`, `ac-1.1`)
2. **Provide labels**: Include `label` properties for human-readable identifiers
3. **Use sort-id**: Enable proper ordering with `sort-id` properties
4. **Document parameters**: Provide clear labels and guidelines for parameters
5. **Include objectives**: Add assessment objectives for each control
6. **Reference sources**: Use back-matter for external references

## Validation

Validate catalogs with oscal-cli:

```bash
oscal-cli validate catalog.json
oscal-cli validate catalog.xml
```

## Official Examples

NIST provides official OSCAL catalogs:

| Catalog | Location |
|---------|----------|
| NIST SP 800-53 Rev 5 | [JSON](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_catalog.json) / [XML](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/xml/NIST_SP-800-53_rev5_catalog.xml) |
| NIST SP 800-53 Rev 4 | [JSON](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev4/json/NIST_SP-800-53_rev4_catalog.json) |
| NIST SP 800-171 Rev 3 | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/nist.gov/SP800-171/rev3) |
| NIST CSF 2.0 | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/nist.gov/CSF/v2.0) |

**Sample catalogs for learning:**
- [Example Catalog](https://github.com/usnistgov/oscal-content/tree/main/examples/catalog)

## Local Examples

Annotated sample catalog in this repository (all formats validated):

| Format | File | Notes |
|--------|------|-------|
| YAML | `examples/catalog/sample-catalog.yaml` | Inline comments explaining each section |
| XML | `examples/catalog/sample-catalog.xml` | Inline comments explaining each section |
| JSON | `examples/catalog/sample-catalog.json` | No comments (JSON limitation) |

These examples demonstrate:
- Organizing controls into groups (families)
- Defining controls with parameters, statements, and guidance
- Adding control enhancements (nested controls)
- Using constraints and select choices for parameters
- Linking to back-matter resources

## Resources

- [OSCAL Catalog Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/control/catalog/)
- [NIST OSCAL Content Repository](https://github.com/usnistgov/oscal-content)

---
name: oscal-profile
description: Use when creating, understanding, or working with OSCAL profiles that select and tailor controls for baselines
---

# OSCAL Profile Model

The Profile model enables selecting, organizing, and tailoring controls from catalogs to create security baselines. Profiles can import from catalogs or other profiles.

**Related skills:**
- `oscal-catalog` - Control definitions that profiles select from
- `oscal-ssp` - System Security Plans that implement profile baselines

## Purpose

A profile:
- **Selects** specific controls from source catalogs
- **Tailors** controls by modifying parameters, adding constraints, or supplementing guidance
- **Organizes** controls into custom groupings
- **Creates baselines** like FedRAMP Low/Moderate/High

## Common Profiles

| Profile | Based On | Description |
|---------|----------|-------------|
| FedRAMP Low | NIST 800-53 | Low-impact cloud systems |
| FedRAMP Moderate | NIST 800-53 | Moderate-impact cloud systems |
| FedRAMP High | NIST 800-53 | High-impact cloud systems |
| CMMC Level 2 | NIST 800-171 | Contractor cybersecurity maturity |

## Document Structure

```yaml
profile:
  uuid: <unique-identifier>
  metadata:
    title: "Baseline Profile"
    version: "1.0.0"
    oscal-version: "1.1.2"
  imports:          # Source catalogs/profiles
    - href: "catalog.json"
      include-controls:
        - with-ids:
            - ac-1
            - ac-2
  merge:            # How to combine imports
    combine:
      method: merge
    as-is: true
  modify:           # Tailoring modifications
    set-parameters: []
    alters: []
  back-matter:
    resources: []
```

## Imports

### Importing from Catalogs

```yaml
imports:
  - href: "https://example.com/oscal/nist-800-53-catalog.json"
    include-controls:
      - with-ids:
          - ac-1
          - ac-2
          - ac-3
```

### Importing from Other Profiles

```yaml
imports:
  - href: "fedramp-moderate-baseline.json"
    include-controls:
      - with-ids:
          - ac-1
```

### Include/Exclude Patterns

**Include all controls:**
```yaml
imports:
  - href: "catalog.json"
    include-all: {}
```

**Include by matching:**
```yaml
imports:
  - href: "catalog.json"
    include-controls:
      - matching:
          - pattern: "ac-*"    # All access control
          - pattern: "sc-*"    # All system/comms protection
```

**Exclude specific controls:**
```yaml
imports:
  - href: "catalog.json"
    include-all: {}
    exclude-controls:
      - with-ids:
          - ac-1    # Exclude this control
```

## Merge Behavior

Control how imported controls are combined:

```yaml
merge:
  combine:
    method: merge    # or "use-first", "keep"
  as-is: true        # Preserve source structure
  flat: true         # Flatten into single list
  custom:            # Custom organization
    groups:
      - id: access
        title: "Access Controls"
        insert-controls:
          - include-controls:
              - with-ids:
                  - ac-1
                  - ac-2
```

### Merge Methods

| Method | Behavior |
|--------|----------|
| `merge` | Combine duplicate controls |
| `use-first` | Keep first occurrence only |
| `keep` | Keep all occurrences |

## Modify (Tailoring)

### Set Parameters

Override parameter values:

```yaml
modify:
  set-parameters:
    - param-id: ac-1_prm_1
      values:
        - "Chief Information Security Officer"
    - param-id: ac-2_prm_1
      select:
        how-many: one-or-more
        choice:
          - annually
          - upon significant change
```

### Alter Controls

Add, remove, or modify control content:

```yaml
modify:
  alters:
    - control-id: ac-1
      adds:
        - position: ending
          parts:
            - id: ac-1_org_smt
              name: item
              prose: "Organization-specific requirement..."
      removes:
        - by-name: guidance
```

### Alter Positions

| Position | Description |
|----------|-------------|
| `before` | Insert before target |
| `after` | Insert after target |
| `starting` | Insert at beginning |
| `ending` | Insert at end |

## Profile Resolution

Profiles are **resolved** to produce a catalog representing the final baseline:

```bash
# Resolve profile to catalog
oscal-cli resolve-profile profile.json -o resolved-catalog.json

# Validate resolved output
oscal-cli validate resolved-catalog.json
```

The resolved catalog:
- Contains only selected controls
- Has all parameter values set
- Includes all modifications applied
- Can be used as input for SSPs

## Layered Profiles

Profiles can build on other profiles:

```
NIST 800-53 Catalog
       ↓
FedRAMP Moderate Profile (selects ~325 controls)
       ↓
Agency-Specific Profile (adds agency requirements)
       ↓
System-Specific Profile (sets organization parameters)
```

## Best Practices

1. **Reference official catalogs**: Use URIs to published OSCAL catalogs
2. **Document tailoring rationale**: Add remarks explaining modifications
3. **Use inheritance**: Build on existing profiles rather than starting fresh
4. **Validate after changes**: Always resolve and validate profiles
5. **Version control**: Track profile changes over time
6. **Set all parameters**: Ensure no undefined parameters remain

## Example: Creating a Custom Baseline

```yaml
profile:
  uuid: "550e8400-e29b-41d4-a716-446655440000"
  metadata:
    title: "Custom Agency Baseline"
    version: "1.0.0"
    oscal-version: "1.1.2"
  imports:
    - href: "https://raw.githubusercontent.com/GSA/fedramp-automation/master/dist/content/rev5/baselines/json/FedRAMP_rev5_MODERATE-baseline-resolved-profile_catalog.json"
      include-all: {}
  modify:
    set-parameters:
      - param-id: ac-1_prm_1
        values:
          - "Agency CISO"
    alters:
      - control-id: ac-1
        adds:
          - position: ending
            parts:
              - name: item
                prose: "Agency-specific: Review annually."
```

## Official Examples

NIST and FedRAMP provide official OSCAL profiles:

| Profile | Location |
|---------|----------|
| NIST 800-53 Rev 5 LOW | [JSON](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_LOW-baseline_profile.json) |
| NIST 800-53 Rev 5 MODERATE | [JSON](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_MODERATE-baseline_profile.json) |
| NIST 800-53 Rev 5 HIGH | [JSON](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_HIGH-baseline_profile.json) |
| NIST 800-53 Rev 5 PRIVACY | [JSON](https://raw.githubusercontent.com/usnistgov/oscal-content/main/nist.gov/SP800-53/rev5/json/NIST_SP-800-53_rev5_PRIVACY-baseline_profile.json) |
| FedRAMP Rev 5 Baselines | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/baselines) |

**Sample profiles for learning:**
- [Example Profiles](https://github.com/usnistgov/oscal-content/tree/main/examples/profile)

## Local Examples

Annotated sample profiles in this repository (all formats validated):

| Format | File | Notes |
|--------|------|-------|
| YAML | `examples/profile/sample-profile.yaml` | Inline comments explaining each section |
| XML | `examples/profile/sample-profile.xml` | Inline comments explaining each section |
| JSON | `examples/profile/sample-profile.json` | No comments (JSON limitation) |

These examples demonstrate:
- Importing controls from NIST SP 800-53 Rev 5
- Selecting specific controls by ID
- Setting organization-defined parameters
- Adding organizational requirements via `alter`

## Resources

- [OSCAL Profile Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/control/profile/)
- [FedRAMP OSCAL Baselines](https://github.com/GSA/fedramp-automation)

---
name: oscal-ssp
description: Use when creating, understanding, or working with OSCAL System Security Plans that document system security implementations
---

# OSCAL System Security Plan (SSP) Model

The SSP model documents how an information system implements security controls from a specific baseline (profile). It's the core artifact for system authorization.

**Related skills:**
- `oscal-profile` - Baselines that define required controls
- `oscal-component-definition` - Reusable component implementations
- `oscal-assessment-plan` - Planning assessments of SSPs

## Purpose

A System Security Plan:
- **Documents the system** boundary, components, and architecture
- **Describes control implementations** for each required control
- **Identifies responsible parties** for security functions
- **Supports authorization** (e.g., FedRAMP ATO)

## Document Structure

```yaml
system-security-plan:
  uuid: <unique-identifier>
  metadata:
    title: "System Name SSP"
    version: "1.0.0"
    oscal-version: "1.2.0"
  import-profile:
    href: "profile.json"
  system-characteristics: {}
  system-implementation: {}
  control-implementation: {}
  back-matter:
    resources: []
```

## Import Profile

Reference the baseline the system implements:

```yaml
import-profile:
  href: "https://example.com/fedramp-moderate-baseline.json"
```

## System Characteristics

### Basic Information

```yaml
system-characteristics:
  system-ids:
    - identifier-type: https://fedramp.gov
      id: "F1234567890"
  system-name: "Enterprise Management System"
  system-name-short: "EMS"
  description: "Cloud-based enterprise management platform"
  props:
    - name: cloud-service-model
      value: "saas"
    - name: cloud-deployment-model
      value: "public-cloud"
  security-sensitivity-level: moderate
  system-information:
    information-types:
      - uuid: "a3c9f07a-1e4d-4b3e-8f5a-6d7c2e9b0f12"
        title: "Administrative Information"
        categorizations:
          - system: https://doi.org/10.6028/NIST.SP.800-60v2r1
            information-type-ids:
              - C.2.8.12
        confidentiality-impact:
          base: moderate
        integrity-impact:
          base: moderate
        availability-impact:
          base: low
```

### Security Impact Levels

```yaml
security-impact-level:
  security-objective-confidentiality: moderate
  security-objective-integrity: moderate
  security-objective-availability: low
```

### Authorization Boundary

```yaml
authorization-boundary:
  description: "The authorization boundary includes..."
  diagrams:
    - uuid: "b4e5f6a7-8c9d-4e0f-a1b2-3c4d5e6f7a8b"
      description: "System boundary diagram"
      links:
        - href: "#boundary-diagram"
          rel: diagram
```

### Network Architecture

```yaml
network-architecture:
  description: "Network architecture description"
  diagrams:
    - uuid: "c5f6a7b8-9d0e-4f1a-b2c3-4d5e6f7a8b9c"
      description: "Network topology"
```

### Data Flow

```yaml
data-flow:
  description: "Data flow description"
  diagrams:
    - uuid: "d6a7b8c9-0e1f-4a2b-c3d4-5e6f7a8b9c0d"
      description: "Data flow diagram"
```

## System Implementation

### Users

```yaml
system-implementation:
  users:
    - uuid: "e7b8c9d0-1f2a-4b3c-d4e5-6f7a8b9c0d1e"
      title: "System Administrator"
      props:
        - name: type
          value: internal
        - name: privilege-level
          value: privileged
      role-ids:
        - system-admin
      authorized-privileges:
        - title: "Full system access"
          functions-performed:
            - "System configuration"
            - "User management"
```

### Components

> **Important:** Every SSP must include a `this-system` component that represents the entire system.
> This component is referenced by `by-components` entries for controls satisfied by the system as a whole
> (e.g., policies, procedures, or planned implementations).

```yaml
system-implementation:
  components:
    # REQUIRED: this-system component must be first
    - uuid: "f8c9d0e1-2a3b-4c4d-e5f6-7a8b9c0d1e2f"
      type: this-system
      title: "Enterprise Management System"
      description: "This component represents the entire system described in this SSP."
      status:
        state: operational
    - uuid: "a9d0e1f2-3b4c-4d5e-f6a7-8b9c0d1e2f3a"
      type: software
      title: "Web Application Server"
      description: "Primary application server"
      status:
        state: operational
      props:
        - name: vendor
          value: "Application Corp"
        - name: version
          value: "3.2.1"
      responsible-roles:
        - role-id: provider
          party-uuids:
            - "b0e1f2a3-4c5d-4e6f-a7b8-9c0d1e2f3a4b"
```

### Inventory Items

```yaml
system-implementation:
  inventory-items:
    - uuid: "c1f2a3b4-5d6e-4f7a-b8c9-0d1e2f3a4b5c"
      description: "Production web server"
      props:
        - name: ipv4-address
          value: "10.0.1.100"
        - name: fqdn
          value: "web01.example.com"
      implemented-components:
        - component-uuid: "a9d0e1f2-3b4c-4d5e-f6a7-8b9c0d1e2f3a"
```

### Leveraged Authorizations

Reference inherited controls from authorized systems:

```yaml
system-implementation:
  leveraged-authorizations:
    - uuid: "d2a3b4c5-6e7f-4a8b-c9d0-1e2f3a4b5c6d"
      title: "AWS GovCloud"
      party-uuid: "e3b4c5d6-7f8a-4b9c-d0e1-2f3a4b5c6d7e"
      date-authorized: "2023-06-15"
      links:
        - href: "https://marketplace.fedramp.gov/..."
          rel: ato-letter
```

## Control Implementation

### Implemented Requirements

```yaml
control-implementation:
  description: "Control implementation for the system"
  implemented-requirements:
    - uuid: "f4c5d6e7-8a9b-4c0d-e1f2-3a4b5c6d7e8f"
      control-id: ac-1
      responsible-roles:
        - role-id: system-admin
      statements:
        - statement-id: ac-1_smt.a
          uuid: "a5d6e7f8-9b0c-4d1e-f2a3-4b5c6d7e8f9a"
          responsible-roles:
            - role-id: isso
          by-components:
            - component-uuid: "a9d0e1f2-3b4c-4d5e-f6a7-8b9c0d1e2f3a"
              uuid: "b6e7f8a9-0c1d-4e2f-a3b4-5c6d7e8f9a0b"
              description: "Access control policy is documented in..."
              implementation-status:
                state: implemented
```

> **Note:** `implementation-status` is only valid as a structured object (`{state: "..."}`) on
> `by-components` entries. It is NOT a valid prop name on `implemented-requirements` or `statements`.

### By-Components

Document which components satisfy control statements:

```yaml
by-components:
  - component-uuid: "c7f8a9b0-1d2e-4f3a-b4c5-6d7e8f9a0b1c"
    uuid: "d8a9b0c1-2e3f-4a4b-c5d6-7e8f9a0b1c2d"
    description: "Database enforces access controls through..."
    implementation-status:
      state: implemented
    export:
      responsibilities:
        - uuid: "e9b0c1d2-3f4a-4b5c-d6e7-8f9a0b1c2d3e"
          description: "Customer must configure user accounts"
          responsible-roles:
            - role-id: customer
```

### Set Parameters

Set organization-specific parameter values:

```yaml
implemented-requirements:
  - control-id: ac-2
    uuid: "f0c1d2e3-4a5b-4c6d-e7f8-9a0b1c2d3e4f"
    set-parameters:
      - param-id: ac-2_prm_1
        values:
          - "30 days"
      - param-id: ac-2_prm_2
        values:
          - "privileged users"
          - "system accounts"
```

### Inherited Controls

Document controls inherited from other systems:

```yaml
by-components:
  - component-uuid: "a1d2e3f4-5b6c-4d7e-f8a9-0b1c2d3e4f5a"
    uuid: "b2e3f4a5-6c7d-4e8f-a9b0-1c2d3e4f5a6b"
    description: "Inherited from AWS GovCloud"
    implementation-status:
      state: implemented
    inherited:
      - uuid: "c3f4a5b6-7d8e-4f9a-b0c1-2d3e4f5a6b7c"
        description: "Physical security controls inherited"
    satisfied:
      - uuid: "d4a5b6c7-8e9f-4a0b-c1d2-3e4f5a6b7c8d"
        description: "Customer responsibility satisfied by..."
```

## Implementation Status Values

| Status | Description |
|--------|-------------|
| `implemented` | Control fully implemented |
| `partial` | Partially implemented |
| `planned` | Implementation planned |
| `alternative` | Alternative implementation |
| `not-applicable` | Control not applicable |

## Parties and Roles

```yaml
metadata:
  parties:
    - uuid: "e5b6c7d8-9f0a-4b1c-d2e3-4f5a6b7c8d9e"
      type: organization
      name: "Organization Name"
    - uuid: "f6c7d8e9-0a1b-4c2d-e3f4-5a6b7c8d9e0f"
      type: person
      name: "John Smith"
      email-addresses:
        - john.smith@example.com
  roles:
    - id: system-owner
      title: "System Owner"
    - id: isso
      title: "Information System Security Officer"
  responsible-parties:
    - role-id: system-owner
      party-uuids:
        - "f6c7d8e9-0a1b-4c2d-e3f4-5a6b7c8d9e0f"
```

## Back Matter

Back matter provides references and supporting documentation. Resources **must** include `rlinks` (resource links) to be valid:

```yaml
back-matter:
  resources:
    - uuid: "a7d8e9f0-1b2c-4d3e-f4a5-6b7c8d9e0f1a"
      title: "Access Control Policy"
      description: "Organizational access control policy document"
      props:
        - name: type
          value: policy
      rlinks:
        - href: "./documents/access-control-policy.pdf"
    - uuid: "b8e9f0a1-2c3d-4e4f-a5b6-7c8d9e0f1a2b"
      title: "Authorization Boundary Diagram"
      description: "System authorization boundary diagram"
      rlinks:
        - href: "./documents/boundary-diagram.png"
          media-type: image/png
```

## Best Practices

1. **Complete all statements**: Address every control statement
2. **Be specific**: Describe actual implementations, not generic text
3. **Map to components**: Use by-components to trace implementations
4. **Set parameters**: Provide specific values for all parameters
5. **Document inheritance**: Clearly identify inherited controls
6. **Include evidence**: Reference supporting documentation in back-matter
7. **Version control**: Maintain SSP version history
8. **Include `this-system`**: Every SSP must have a component with `type: this-system`
9. **Use `implementation-status` correctly**: Only as `{state: "..."}` on `by-components`, never as a prop

## Validation

```bash
# Validate SSP structure
oscal-cli validate ssp.json
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample SSP | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/ssp) |
| FedRAMP SSP Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL SSP Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/implementation/ssp/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

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
    oscal-version: "1.1.2"
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
      - uuid: info-type-uuid
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
    - uuid: diagram-uuid
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
    - uuid: network-diagram-uuid
      description: "Network topology"
```

### Data Flow

```yaml
data-flow:
  description: "Data flow description"
  diagrams:
    - uuid: dataflow-uuid
      description: "Data flow diagram"
```

## System Implementation

### Users

```yaml
system-implementation:
  users:
    - uuid: user-uuid
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

```yaml
system-implementation:
  components:
    - uuid: component-uuid
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
            - party-uuid
```

### Inventory Items

```yaml
system-implementation:
  inventory-items:
    - uuid: inventory-uuid
      description: "Production web server"
      props:
        - name: ipv4-address
          value: "10.0.1.100"
        - name: fqdn
          value: "web01.example.com"
      implemented-components:
        - component-uuid: component-uuid
```

### Leveraged Authorizations

Reference inherited controls from authorized systems:

```yaml
system-implementation:
  leveraged-authorizations:
    - uuid: leveraged-uuid
      title: "AWS GovCloud"
      party-uuid: aws-party-uuid
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
    - uuid: req-uuid
      control-id: ac-1
      props:
        - name: implementation-status
          value: implemented
      responsible-roles:
        - role-id: system-admin
      statements:
        - statement-id: ac-1_smt.a
          uuid: stmt-uuid
          props:
            - name: implementation-status
              value: implemented
          responsible-roles:
            - role-id: isso
          by-components:
            - component-uuid: policy-component-uuid
              uuid: by-comp-uuid
              description: "Access control policy is documented in..."
```

### By-Components

Document which components satisfy control statements:

```yaml
by-components:
  - component-uuid: database-uuid
    uuid: by-comp-uuid
    description: "Database enforces access controls through..."
    props:
      - name: implementation-status
        value: implemented
    implementation-status:
      state: implemented
    export:
      responsibilities:
        - uuid: resp-uuid
          description: "Customer must configure user accounts"
          responsible-roles:
            - role-id: customer
```

### Set Parameters

Set organization-specific parameter values:

```yaml
implemented-requirements:
  - control-id: ac-2
    uuid: req-uuid
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
  - component-uuid: leveraged-system-uuid
    uuid: by-comp-uuid
    description: "Inherited from AWS GovCloud"
    inherited:
      - uuid: inherited-uuid
        description: "Physical security controls inherited"
    satisfied:
      - uuid: satisfied-uuid
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
    - uuid: org-uuid
      type: organization
      name: "Organization Name"
    - uuid: person-uuid
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
        - person-uuid
```

## Best Practices

1. **Complete all statements**: Address every control statement
2. **Be specific**: Describe actual implementations, not generic text
3. **Map to components**: Use by-components to trace implementations
4. **Set parameters**: Provide specific values for all parameters
5. **Document inheritance**: Clearly identify inherited controls
6. **Include evidence**: Reference supporting documentation in back-matter
7. **Version control**: Maintain SSP version history

## Validation

```bash
# Validate SSP structure
oscal-cli validate ssp.json

# Check against profile
oscal-cli validate ssp.json --profile baseline.json
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample SSP | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/ssp) |
| FedRAMP SSP Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL SSP Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/implementation/ssp/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

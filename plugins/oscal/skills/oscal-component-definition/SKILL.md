---
name: oscal-component-definition
description: Use when creating, understanding, or working with OSCAL component definitions that describe control implementations for reusable components
---

# OSCAL Component Definition Model

The Component Definition model describes how components (hardware, software, services, policies, processes) can satisfy security controls. Component definitions are reusable across multiple systems.

**Related skills:**
- `oscal-ssp` - System Security Plans that incorporate components
- `oscal-profile` - Baselines that define required controls

## Purpose

Component definitions:
- **Document control implementations** for products/services
- **Enable reuse** across multiple system security plans
- **Support supply chain** security documentation
- **Describe configuration** options for security compliance

## Component Types

| Type | Examples |
|------|----------|
| `software` | Operating systems, applications, databases |
| `hardware` | Servers, network devices, HSMs |
| `service` | Cloud services, managed services |
| `policy` | Security policies, procedures |
| `process` | Operational processes, workflows |
| `procedure` | Step-by-step instructions |
| `plan` | Contingency plans, incident response |
| `guidance` | Security guidance documents |
| `standard` | Technical standards |
| `validation` | FIPS 140-2 certificates, FedRAMP ATOs |

## Document Structure

```yaml
component-definition:
  uuid: <unique-identifier>
  metadata:
    title: "Component Name"
    version: "1.0.0"
    oscal-version: "1.1.2"
  import-component-definitions: []  # Reference other components
  components:
    - uuid: component-uuid
      type: software
      title: "Product Name"
      description: "Product description"
      control-implementations: []
  capabilities: []                  # Optional capability groupings
  back-matter:
    resources: []
```

## Components

### Basic Component Definition

```yaml
components:
  - uuid: "550e8400-e29b-41d4-a716-446655440000"
    type: software
    title: "Enterprise Database Server"
    description: "Relational database management system"
    props:
      - name: vendor
        value: "Database Corp"
      - name: version
        value: "15.2"
    protocols:
      - uuid: protocol-uuid
        name: https
        port-ranges:
          - start: 443
            end: 443
            transport: TCP
    control-implementations:
      - uuid: impl-uuid
        source: "profile.json"
        description: "Controls implemented by this component"
        implemented-requirements: []
```

### Control Implementations

```yaml
control-implementations:
  - uuid: "impl-uuid"
    source: "https://example.com/fedramp-moderate-profile.json"
    description: "FedRAMP Moderate controls implemented"
    props:
      - name: framework
        value: "FedRAMP"
    implemented-requirements:
      - uuid: "req-uuid"
        control-id: ac-2
        description: "This component provides..."
        props:
          - name: implementation-status
            value: implemented
        responsible-roles:
          - role-id: provider
        statements:
          - statement-id: ac-2_smt.a
            uuid: stmt-uuid
            description: "Account management is provided through..."
```

### Implementation Status

| Status | Description |
|--------|-------------|
| `implemented` | Fully implemented by this component |
| `partial` | Partially implemented, needs supplementation |
| `planned` | Implementation planned for future |
| `alternative` | Alternative implementation approach |
| `not-applicable` | Control not applicable to this component |

## Responsible Roles

Define who is responsible for implementation aspects:

```yaml
components:
  - uuid: component-uuid
    responsible-roles:
      - role-id: provider
        party-uuids:
          - party-uuid
        remarks: "Software vendor responsibility"
      - role-id: customer
        remarks: "Customer configuration responsibility"
```

## Capabilities

Group related control implementations:

```yaml
capabilities:
  - uuid: capability-uuid
    name: "Encryption at Rest"
    description: "Data encryption capabilities"
    control-implementations:
      - uuid: encryption-impl-uuid
        source: "profile.json"
        implemented-requirements:
          - control-id: sc-28
            description: "AES-256 encryption for stored data"
    incorporates-components:
      - component-uuid: component-uuid
        description: "Database encryption module"
```

## Import Component Definitions

Reference external component definitions:

```yaml
import-component-definitions:
  - href: "https://example.com/vendor-component.json"
```

## Inheritance and Responsibility

Document shared responsibilities:

```yaml
implemented-requirements:
  - control-id: ac-2
    uuid: req-uuid
    description: "Account management implementation"
    props:
      - name: responsibility
        value: shared
    statements:
      - statement-id: ac-2_smt.a
        uuid: stmt-uuid
        description: "Provider manages service accounts"
        props:
          - name: responsibility
            value: provider
      - statement-id: ac-2_smt.b
        uuid: stmt-uuid-2
        description: "Customer manages user accounts"
        props:
          - name: responsibility
            value: customer
```

## Set Parameters

Components can set parameter values:

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
          - "privileged accounts"
          - "service accounts"
```

## Best Practices

1. **Be specific about coverage**: Clearly state what the component does and doesn't implement
2. **Document shared responsibility**: Identify customer vs. provider responsibilities
3. **Include configuration guidance**: Document settings required for compliance
4. **Reference baselines**: Link to specific profiles/baselines
5. **Version components**: Track component versions in metadata
6. **Use standard types**: Follow OSCAL component type conventions

## Example: Cloud Service Component

```yaml
component-definition:
  uuid: "550e8400-e29b-41d4-a716-446655440000"
  metadata:
    title: "Cloud Storage Service"
    version: "2.0.0"
    oscal-version: "1.1.2"
  components:
    - uuid: "cloud-storage-uuid"
      type: service
      title: "Enterprise Cloud Storage"
      description: "Object storage service with encryption"
      props:
        - name: service-model
          value: "IaaS"
      responsible-roles:
        - role-id: provider
          remarks: "Cloud service provider"
      control-implementations:
        - uuid: "fedramp-impl"
          source: "fedramp-moderate.json"
          description: "FedRAMP Moderate Implementation"
          implemented-requirements:
            - uuid: "sc-28-impl"
              control-id: sc-28
              description: "All data encrypted at rest using AES-256"
              props:
                - name: implementation-status
                  value: implemented
            - uuid: "sc-13-impl"
              control-id: sc-13
              description: "FIPS 140-2 validated cryptographic modules"
              props:
                - name: implementation-status
                  value: implemented
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample Component Definitions | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/component-definition) |
| FedRAMP Component Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL Component Definition Reference](https://pages.nist.gov/OSCAL/concepts/layer/implementation/component-definition/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

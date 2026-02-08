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
    oscal-version: "1.2.0"
  import-component-definitions: []  # Reference other components
  components:
    - uuid: "a1b2c3d4-e5f6-4a7b-8c9d-0e1f2a3b4c5d"
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
      - uuid: "7a8b9c0d-1e2f-4a3b-c4d5-e6f7a8b9c0d1"
        name: https
        port-ranges:
          - start: 443
            end: 443
            transport: TCP
    control-implementations:
      - uuid: "8b9c0d1e-2f3a-4b4c-d5e6-f7a8b9c0d1e2"
        source: "profile.json"
        description: "Controls implemented by this component"
        implemented-requirements: []
```

### Control Implementations

```yaml
control-implementations:
  - uuid: "9c0d1e2f-3a4b-4c5d-e6f7-a8b9c0d1e2f3"
    source: "https://example.com/fedramp-moderate-profile.json"
    description: "FedRAMP Moderate controls implemented"
    props:
      - name: framework
        value: "FedRAMP"
    implemented-requirements:
      - uuid: "0d1e2f3a-4b5c-4d6e-f7a8-b9c0d1e2f3a4"
        control-id: ac-2
        description: "This component provides..."
        responsible-roles:
          - role-id: provider
        statements:
          - statement-id: ac-2_smt.a
            uuid: "1e2f3a4b-5c6d-4e7f-a8b9-c0d1e2f3a4b5"
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
  - uuid: "2f3a4b5c-6d7e-4f8a-b9c0-d1e2f3a4b5c6"
    responsible-roles:
      - role-id: provider
        party-uuids:
          - "3a4b5c6d-7e8f-4a9b-c0d1-e2f3a4b5c6d7"
        remarks: "Software vendor responsibility"
      - role-id: customer
        remarks: "Customer configuration responsibility"
```

## Capabilities

Group related control implementations:

```yaml
capabilities:
  - uuid: "4b5c6d7e-8f9a-4b0c-d1e2-f3a4b5c6d7e8"
    name: "Encryption at Rest"
    description: "Data encryption capabilities"
    control-implementations:
      - uuid: "5c6d7e8f-9a0b-4c1d-e2f3-a4b5c6d7e8f9"
        source: "profile.json"
        implemented-requirements:
          - control-id: sc-28
            description: "AES-256 encryption for stored data"
    incorporates-components:
      - component-uuid: "2f3a4b5c-6d7e-4f8a-b9c0-d1e2f3a4b5c6"
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
    uuid: "6d7e8f9a-0b1c-4d2e-f3a4-b5c6d7e8f9a0"
    description: "Account management implementation"
    props:
      - name: responsibility
        value: shared
    statements:
      - statement-id: ac-2_smt.a
        uuid: "7e8f9a0b-1c2d-4e3f-a4b5-c6d7e8f9a0b1"
        description: "Provider manages service accounts"
        props:
          - name: responsibility
            value: provider
      - statement-id: ac-2_smt.b
        uuid: "8f9a0b1c-2d3e-4f4a-b5c6-d7e8f9a0b1c2"
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
    uuid: "9a0b1c2d-3e4f-4a5b-c6d7-e8f9a0b1c2d3"
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
    oscal-version: "1.2.0"
  components:
    - uuid: "0b1c2d3e-4f5a-4b6c-d7e8-f9a0b1c2d3e4"
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
        - uuid: "1c2d3e4f-5a6b-4c7d-e8f9-a0b1c2d3e4f5"
          source: "fedramp-moderate.json"
          description: "FedRAMP Moderate Implementation"
          implemented-requirements:
            - uuid: "2d3e4f5a-6b7c-4d8e-f9a0-b1c2d3e4f5a6"
              control-id: sc-28
              description: "All data encrypted at rest using AES-256"
            - uuid: "3e4f5a6b-7c8d-4e9f-a0b1-c2d3e4f5a6b7"
              control-id: sc-13
              description: "FIPS 140-2 validated cryptographic modules"
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample Component Definitions | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/component-definition) |
| FedRAMP Component Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL Component Definition Reference](https://pages.nist.gov/OSCAL/concepts/layer/implementation/component-definition/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

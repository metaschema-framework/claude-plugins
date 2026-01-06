---
name: oscal-assessment-plan
description: Use when creating, understanding, or working with OSCAL Security Assessment Plans (SAP) that define assessment scope and activities
---

# OSCAL Assessment Plan (SAP) Model

The Assessment Plan model documents how a security assessment will be conducted, including scope, methodology, schedule, and assessment activities.

**Related skills:**
- `oscal-ssp` - System Security Plan being assessed
- `oscal-assessment-results` - Results from executing the plan
- `oscal-poam` - Remediation tracking for findings

## Purpose

An Assessment Plan:
- **Defines assessment scope** and objectives
- **Identifies assessment methods** (examine, interview, test)
- **Specifies schedule and resources**
- **Documents assessment activities** for each control
- **Supports repeatable assessments**

## Document Structure

```yaml
assessment-plan:
  uuid: <unique-identifier>
  metadata:
    title: "System Assessment Plan"
    version: "1.0.0"
    oscal-version: "1.1.2"
  import-ssp:
    href: "ssp.json"
  local-definitions: {}
  terms-and-conditions: {}
  reviewed-controls: {}
  assessment-subjects: []
  assessment-assets: {}
  tasks: []
  back-matter:
    resources: []
```

## Import SSP

Reference the System Security Plan being assessed:

```yaml
import-ssp:
  href: "https://example.com/system-ssp.json"
```

## Local Definitions

Define assessment-specific components, users, and objectives:

```yaml
local-definitions:
  components:
    - uuid: assessment-tool-uuid
      type: software
      title: "Vulnerability Scanner"
      description: "Automated vulnerability scanning tool"
  inventory-items:
    - uuid: scanner-instance-uuid
      description: "Scanner deployment"
  users:
    - uuid: assessor-uuid
      title: "Lead Assessor"
      props:
        - name: type
          value: external
  objectives-and-methods:
    - control-id: ac-2
      description: "Account management assessment"
      parts:
        - id: ac-2_obj.1
          name: objective
          prose: "Verify account types are defined"
        - id: ac-2_method.1
          name: method
          props:
            - name: method
              value: EXAMINE
          parts:
            - name: objects
              prose: "Account management policy and procedures"
```

## Assessment Methods

OSCAL supports three primary assessment methods:

| Method | Description | Activities |
|--------|-------------|------------|
| `EXAMINE` | Review documentation | Policy review, configuration analysis |
| `INTERVIEW` | Discuss with personnel | Staff interviews, role-based discussions |
| `TEST` | Active testing | Vulnerability scanning, penetration testing |

### Objectives and Methods

```yaml
objectives-and-methods:
  - control-id: ac-2
    description: "Account management assessment objectives"
    props:
      - name: priority
        value: high
    parts:
      - id: ac-2_obj.a
        name: objective
        prose: "Account types are identified and documented"
        parts:
          - id: ac-2_obj.a.1
            name: objective
            prose: "Privileged accounts are identified"
      - id: ac-2_method.examine
        name: method
        props:
          - name: method
            value: EXAMINE
        parts:
          - name: objects
            prose: |
              - Access control policy
              - Account inventory
              - System configuration
        links:
          - href: "#ac-2_obj.a"
            rel: objective
```

## Terms and Conditions

Define assessment rules and agreements:

```yaml
terms-and-conditions:
  parts:
    - id: rules-of-engagement
      name: rules-of-engagement
      title: "Rules of Engagement"
      prose: |
        1. All testing during approved windows
        2. No destructive testing without approval
        3. Immediate notification of critical findings
    - id: assumptions
      name: assumptions
      prose: "Assessment assumes production-like environment"
    - id: limitations
      name: limitations
      prose: "Physical security assessment excluded"
```

## Reviewed Controls

Specify which controls are in scope:

```yaml
reviewed-controls:
  control-selections:
    - description: "All FedRAMP Moderate controls"
      include-all: {}
    - description: "Specific high-priority controls"
      include-controls:
        - control-id: ac-2
        - control-id: ac-3
        - control-id: ia-2
  control-objective-selections:
    - include-all: {}
```

### Exclude Controls

```yaml
reviewed-controls:
  control-selections:
    - include-all: {}
      exclude-controls:
        - control-id: pe-1  # Physical security excluded
        - control-id: pe-2
```

## Assessment Subjects

Define what is being assessed:

```yaml
assessment-subjects:
  - type: component
    description: "All system components"
    include-all: {}
  - type: inventory-item
    description: "Production servers"
    include-subjects:
      - subject-uuid: server-uuid
        type: inventory-item
  - type: location
    description: "Primary data center"
    include-subjects:
      - subject-uuid: datacenter-uuid
        type: location
```

### Subject Types

| Type | Description |
|------|-------------|
| `component` | System components from SSP |
| `inventory-item` | Specific inventory items |
| `location` | Physical locations |
| `party` | Organizations or individuals |
| `user` | System users |

## Assessment Assets

Document resources used for assessment:

```yaml
assessment-assets:
  components:
    - uuid: scanner-uuid
      type: software
      title: "Nessus Scanner"
      description: "Vulnerability scanning tool"
      status:
        state: operational
  assessment-platforms:
    - uuid: platform-uuid
      title: "Assessment Platform"
      uses-components:
        - component-uuid: scanner-uuid
```

## Tasks

Define scheduled assessment activities:

```yaml
tasks:
  - uuid: task-uuid
    type: action
    title: "Vulnerability Scan"
    description: "Automated vulnerability assessment"
    timing:
      within-date-range:
        start: "2024-03-01T00:00:00Z"
        end: "2024-03-15T23:59:59Z"
    dependencies:
      - task-uuid: prereq-task-uuid
    associated-activities:
      - activity-uuid: scan-activity-uuid
        subjects:
          - type: component
            include-all: {}
    responsible-roles:
      - role-id: assessor
    remarks: "Weekly scans during assessment window"
```

### Task Types

| Type | Description |
|------|-------------|
| `milestone` | Key dates/checkpoints |
| `action` | Assessment activities |

## Activities

Define specific assessment activities:

```yaml
local-definitions:
  activities:
    - uuid: scan-activity-uuid
      title: "Automated Vulnerability Scan"
      description: "Execute authenticated vulnerability scan"
      props:
        - name: method
          value: TEST
      steps:
        - uuid: step-1-uuid
          title: "Configure scan credentials"
          description: "Set up authenticated scanning"
        - uuid: step-2-uuid
          title: "Execute scan"
          description: "Run full vulnerability scan"
        - uuid: step-3-uuid
          title: "Review results"
          description: "Analyze and document findings"
      related-controls:
        control-selections:
          - include-controls:
              - control-id: ra-5
              - control-id: si-2
```

## Best Practices

1. **Be comprehensive**: Cover all in-scope controls
2. **Define clear objectives**: Make success criteria measurable
3. **Specify methods**: Document examine/interview/test activities
4. **Include schedule**: Define assessment windows and milestones
5. **Document tools**: List assessment platforms and tools
6. **Set expectations**: Clear rules of engagement and limitations
7. **Enable traceability**: Link activities to control objectives

## Validation

```bash
# Validate assessment plan
oscal-cli validate assessment-plan.json

# Validate SSP reference
oscal-cli validate assessment-plan.json --ssp system-ssp.json
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample Assessment Plans | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/ap) |
| FedRAMP SAP Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL Assessment Plan Reference](https://pages.nist.gov/OSCAL/concepts/layer/assessment/assessment-plan/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

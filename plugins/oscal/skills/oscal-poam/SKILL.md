---
name: oscal-poam
description: Use when creating, understanding, or working with OSCAL Plan of Action and Milestones (POA&M) that track security finding remediation
---

# OSCAL Plan of Action and Milestones (POA&M) Model

The POA&M model tracks identified weaknesses and the planned remediation activities. It documents how and when risks will be addressed.

**Related skills:**
- `oscal-assessment-results` - Findings that generate POA&M entries
- `oscal-ssp` - System with identified weaknesses

## Purpose

A POA&M:
- **Tracks remediation** of identified weaknesses
- **Documents milestones** for remediation activities
- **Records risk decisions** (accept, mitigate, transfer)
- **Supports authorization maintenance**
- **Provides visibility** into security posture improvements

## Document Structure

```yaml
plan-of-action-and-milestones:
  uuid: <unique-identifier>
  metadata:
    title: "System POA&M"
    version: "1.0.0"
    oscal-version: "1.2.0"
  import-ssp:
    href: "ssp.json"
  system-id:
    identifier-type: https://fedramp.gov
    id: "F1234567890"
  local-definitions: {}
  observations: []
  risks: []
  findings: []
  poam-items: []
  back-matter:
    resources: []
```

## Import SSP

Reference the system's SSP:

```yaml
import-ssp:
  href: "https://example.com/system-ssp.json"
```

## POA&M Items

The core tracking element:

```yaml
poam-items:
  - uuid: poam-item-uuid
    title: "Password Complexity Remediation"
    description: "Implement stronger password requirements"
    props:
      - name: poam-id
        value: "V-2024-001"
      - name: priority
        value: high
    origins:
      - actors:
          - type: party
            actor-uuid: assessor-uuid
            role-id: assessor
    related-findings:
      - finding-uuid: finding-uuid
    related-observations:
      - observation-uuid: observation-uuid
    related-risks:
      - risk-uuid: risk-uuid
    remarks: "Scheduled for Q2 2024 implementation"
```

### POA&M Item Properties

Common properties for tracking:

```yaml
props:
  - name: poam-id
    value: "V-2024-001"
  - name: priority
    value: high
  - name: vendor-dependency
    value: "true"
  - name: deviation
    value: "operational-requirement"
  - name: planned-completion-date
    value: "2024-06-30"
  - name: original-detection-date
    value: "2024-03-10"
  - name: scheduled-completion-date
    value: "2024-06-30"
  - name: actual-completion-date
    value: "2024-05-15"
```

## Risks in POA&M

Document associated risks:

```yaml
risks:
  - uuid: risk-uuid
    title: "Weak Authentication"
    description: "Risk from inadequate password controls"
    statement: "Current password policy allows weak credentials"
    props:
      - name: risk-status
        value: open
      - name: risk-level
        value: high
      - name: likelihood
        value: moderate
      - name: impact
        value: high
    status: remediating
    characterizations:
      - origin:
          actors:
            - type: party
              actor-uuid: assessor-uuid
        facets:
          - name: likelihood
            system: https://fedramp.gov
            value: moderate
          - name: impact
            system: https://fedramp.gov
            value: high
          - name: risk
            system: https://fedramp.gov
            value: high
    mitigating-factors:
      - uuid: mf-uuid
        description: "MFA partially mitigates credential risk"
        subjects:
          - subject-uuid: mfa-component-uuid
            type: component
    deadline: "2024-06-30T23:59:59Z"
    remediations:
      - uuid: remediation-uuid
        lifecycle: planned
        title: "Update Password Policy"
        description: "Implement NIST 800-63B compliant passwords"
        tasks:
          - uuid: task-uuid
            type: milestone
            title: "Policy Review Complete"
            timing:
              within-date-range:
                start: "2024-04-01"
                end: "2024-04-15"
    risk-log:
      entries:
        - uuid: log-entry-uuid
          title: "Risk Identified"
          start: "2024-03-10T14:30:00Z"
          status-change: open
          description: "Identified during annual assessment"
```

### Risk Status Values

| Status | Description |
|--------|-------------|
| `open` | Newly identified, needs attention |
| `investigating` | Under review |
| `remediating` | Fix in progress |
| `deviation-requested` | Exception requested |
| `deviation-approved` | Exception granted |
| `closed` | Issue resolved |

## Remediations

Document planned remediation activities:

```yaml
remediations:
  - uuid: remediation-uuid
    lifecycle: planned
    title: "Password Policy Enhancement"
    description: "Update password complexity requirements"
    props:
      - name: estimated-completion
        value: "2024-06-30"
    origins:
      - actors:
          - type: party
            actor-uuid: system-owner-uuid
            role-id: system-owner
    required-assets:
      - uuid: asset-uuid
        subjects:
          - subject-uuid: admin-team-uuid
            type: party
        description: "IT administrators for implementation"
        remarks: "2 FTE for 2 weeks"
    tasks:
      - uuid: task-1-uuid
        type: milestone
        title: "Requirements Definition"
        timing:
          within-date-range:
            start: "2024-04-01"
            end: "2024-04-07"
      - uuid: task-2-uuid
        type: action
        title: "Policy Implementation"
        timing:
          within-date-range:
            start: "2024-04-08"
            end: "2024-04-30"
        dependencies:
          - task-uuid: task-1-uuid
      - uuid: task-3-uuid
        type: milestone
        title: "Verification Complete"
        timing:
          within-date-range:
            start: "2024-05-01"
            end: "2024-05-15"
```

### Remediation Lifecycle

| Lifecycle | Description |
|-----------|-------------|
| `recommendation` | Suggested approach |
| `planned` | Approved for implementation |
| `implemented` | Changes deployed |
| `completed` | Verified effective |

## Observations

Include relevant observations:

```yaml
observations:
  - uuid: observation-uuid
    title: "Password Policy Review"
    description: "Current password configuration settings"
    methods:
      - EXAMINE
    types:
      - finding
    subjects:
      - subject-uuid: auth-system-uuid
        type: component
    collected: "2024-03-10T14:30:00Z"
    expires: "2025-03-10T14:30:00Z"
```

## Findings

Document findings linked to POA&M items:

```yaml
findings:
  - uuid: finding-uuid
    title: "Weak Password Policy"
    description: "Password complexity does not meet requirements"
    target:
      type: objective-id
      target-id: ia-5.1_obj.a
      status:
        state: not-satisfied
    related-observations:
      - observation-uuid: observation-uuid
    related-risks:
      - risk-uuid: risk-uuid
```

## Risk Log

Track risk status changes over time:

```yaml
risk-log:
  entries:
    - uuid: entry-1-uuid
      title: "Initial Detection"
      start: "2024-03-10T14:30:00Z"
      status-change: open
      description: "Identified during annual assessment"
    - uuid: entry-2-uuid
      title: "Remediation Started"
      start: "2024-04-01T09:00:00Z"
      status-change: remediating
      description: "Project kickoff completed"
    - uuid: entry-3-uuid
      title: "Implementation Complete"
      start: "2024-05-15T16:00:00Z"
      status-change: closed
      description: "Password policy updated, verified effective"
      related-responses:
        - response-uuid: remediation-uuid
```

## Local Definitions

Define POA&M-specific items:

```yaml
local-definitions:
  components:
    - uuid: remediation-tool-uuid
      type: software
      title: "Configuration Management Tool"
  inventory-items:
    - uuid: affected-server-uuid
      description: "Server requiring updates"
```

## Best Practices

1. **Unique identifiers**: Assign tracking IDs (e.g., V-2024-001)
2. **Set priorities**: Categorize by risk level
3. **Define milestones**: Create measurable progress checkpoints
4. **Document dependencies**: Note vendor or resource dependencies
5. **Track status changes**: Maintain risk log history
6. **Set realistic dates**: Account for change management
7. **Link to evidence**: Reference supporting documentation
8. **Regular updates**: Keep POA&M current

## FedRAMP POA&M Requirements

For FedRAMP systems:

```yaml
props:
  - name: deviation-type
    ns: https://fedramp.gov/ns/oscal
    value: "operational-requirement"
  - name: vendor-dependency
    ns: https://fedramp.gov/ns/oscal
    value: "true"
  - name: false-positive
    ns: https://fedramp.gov/ns/oscal
    value: "false"
  - name: risk-adjustment
    ns: https://fedramp.gov/ns/oscal
    value: "moderate"
```

## Validation

```bash
# Validate POA&M
oscal-cli validate poam.json

# Validate POA&M structure
oscal-cli validate poam.json
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample POA&M | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/poam) |
| FedRAMP POA&M Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL POA&M Model Reference](https://pages.nist.gov/OSCAL/concepts/layer/assessment/poam/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

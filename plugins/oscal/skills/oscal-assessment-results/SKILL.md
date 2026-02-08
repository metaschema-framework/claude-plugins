---
name: oscal-assessment-results
description: Use when creating, understanding, or working with OSCAL Security Assessment Results (SAR) that document assessment findings
---

# OSCAL Assessment Results (SAR) Model

The Assessment Results model documents findings from security assessments, including observations, risks, and evidence. It captures what was found when executing an assessment plan.

**Related skills:**
- `oscal-assessment-plan` - Plan that guided the assessment
- `oscal-ssp` - System that was assessed
- `oscal-poam` - Remediation tracking for findings

## Purpose

Assessment Results:
- **Document findings** from assessment activities
- **Record observations** about control implementation
- **Identify risks** requiring remediation
- **Provide evidence** supporting conclusions
- **Support continuous monitoring** and periodic assessments

## Document Structure

```yaml
assessment-results:
  uuid: <unique-identifier>
  metadata:
    title: "System Assessment Results"
    version: "1.0.0"
    oscal-version: "1.2.0"
  import-ap:
    href: "assessment-plan.json"
  local-definitions: {}
  results:
    - uuid: result-uuid
      title: "Annual Assessment Results"
      start: "2024-03-01T00:00:00Z"
      end: "2024-03-15T23:59:59Z"
      reviewed-controls: {}
      findings: []
      observations: []
      risks: []
  back-matter:
    resources: []
```

## Import Assessment Plan

Reference the assessment plan that was executed:

```yaml
import-ap:
  href: "https://example.com/assessment-plan.json"
```

## Results

Each result represents an assessment period:

```yaml
results:
  - uuid: "result-uuid"
    title: "Q1 2024 Assessment"
    description: "Quarterly security assessment"
    start: "2024-03-01T00:00:00Z"
    end: "2024-03-15T23:59:59Z"
    props:
      - name: assessment-type
        value: periodic
    reviewed-controls:
      control-selections:
        - include-all: {}
    attestations:
      - responsible-parties:
          - role-id: lead-assessor
            party-uuids:
              - assessor-uuid
        parts:
          - name: attestation-statement
            prose: "This assessment was conducted in accordance with..."
```

## Observations

Observations record what was found during assessment:

```yaml
observations:
  - uuid: observation-uuid
    title: "Password Policy Configuration"
    description: "Observed password complexity settings"
    methods:
      - EXAMINE
      - TEST
    types:
      - finding
    origins:
      - actors:
          - type: party
            actor-uuid: assessor-uuid
            role-id: assessor
    subjects:
      - subject-uuid: system-component-uuid
        type: component
    relevant-evidence:
      - href: "#evidence-uuid"
        description: "Screenshot of password policy"
    collected: "2024-03-10T14:30:00Z"
    expires: "2025-03-10T14:30:00Z"
```

### Observation Types

| Type | Description |
|------|-------------|
| `finding` | Assessment finding |
| `historic` | Historical observation |
| `ssp-statement-issue` | SSP accuracy issue |

### Methods

| Method | Description |
|--------|-------------|
| `EXAMINE` | Documentation review |
| `INTERVIEW` | Personnel discussion |
| `TEST` | Active testing |

## Findings

Findings link observations to control objectives:

```yaml
findings:
  - uuid: finding-uuid
    title: "Inadequate Password Complexity"
    description: "Password policy does not meet requirements"
    target:
      type: objective-id
      target-id: ia-5.1_obj.a
      title: "Password Complexity"
      description: "Password complexity assessment"
      props:
        - name: target-level
          value: objective
      status:
        state: not-satisfied
        reason: "Password length requirement not met"
      implementation-status:
        state: partial
    origins:
      - actors:
          - type: party
            actor-uuid: assessor-uuid
            role-id: assessor
    related-observations:
      - observation-uuid: observation-uuid
    related-risks:
      - risk-uuid: risk-uuid
```

### Finding Status

| State | Description |
|-------|-------------|
| `satisfied` | Objective met |
| `not-satisfied` | Objective not met |

### Target Types

| Type | Description |
|------|-------------|
| `statement-id` | Control statement |
| `objective-id` | Control objective |
| `control-id` | Full control |

## Risks

Document identified risks:

```yaml
risks:
  - uuid: risk-uuid
    title: "Weak Authentication Risk"
    description: "Risk of unauthorized access due to weak passwords"
    statement: "Weak password policy increases risk of credential compromise"
    props:
      - name: risk-level
        value: high
      - name: likelihood
        value: moderate
      - name: impact
        value: high
    status: open
    origins:
      - actors:
          - type: party
            actor-uuid: assessor-uuid
            role-id: assessor
    threat-ids:
      - system: https://example.com/threat-catalog
        href: "#T-1234"
        id: T-1234
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
    mitigating-factors:
      - uuid: factor-uuid
        description: "Multi-factor authentication partially mitigates"
    response:
      lifecycle: planned
      title: "Password Policy Update"
      description: "Implement stronger password requirements"
    related-observations:
      - observation-uuid: observation-uuid
```

### Risk Status

| Status | Description |
|--------|-------------|
| `open` | Risk requires attention |
| `investigating` | Under investigation |
| `remediating` | Remediation in progress |
| `deviation-requested` | Exception requested |
| `deviation-approved` | Exception approved |
| `closed` | Risk addressed |

## Evidence

Document supporting evidence in back-matter:

```yaml
back-matter:
  resources:
    - uuid: evidence-uuid
      title: "Password Policy Screenshot"
      description: "Screenshot of system password configuration"
      props:
        - name: type
          value: screenshot
      rlinks:
        - href: "./evidence/password-config.png"
          media-type: image/png
    - uuid: scan-results-uuid
      title: "Vulnerability Scan Results"
      description: "Nessus scan output"
      rlinks:
        - href: "./evidence/scan-2024-03-10.pdf"
          media-type: application/pdf
```

## Local Definitions

Define assessment-specific items:

```yaml
local-definitions:
  components:
    - uuid: scanner-uuid
      type: software
      title: "Assessment Scanner"
  inventory-items:
    - uuid: evidence-server-uuid
      description: "Evidence collection server"
  users:
    - uuid: lead-assessor-uuid
      title: "Lead Assessor"
```

## Attestations

Record assessor attestations:

```yaml
attestations:
  - responsible-parties:
      - role-id: lead-assessor
        party-uuids:
          - assessor-uuid
    parts:
      - name: authorization-statement
        prose: "This assessment was authorized by..."
      - name: independence-statement
        prose: "The assessment team maintains independence..."
      - name: methodology-statement
        prose: "Assessment conducted per NIST SP 800-53A..."
```

## Best Practices

1. **Link to observations**: Connect findings to specific observations
2. **Document evidence**: Include references to supporting evidence
3. **Quantify risks**: Use consistent risk rating methodology
4. **Be specific**: Document exact configuration values, not generalizations
5. **Include timestamps**: Record when observations were made
6. **Set expiration**: Define evidence validity periods
7. **Track origins**: Document who made each observation

## Continuous Monitoring

For continuous monitoring, create incremental results:

```yaml
results:
  - uuid: monthly-result-uuid
    title: "March 2024 Continuous Monitoring"
    description: "Monthly automated assessment"
    start: "2024-03-01T00:00:00Z"
    end: "2024-03-31T23:59:59Z"
    props:
      - name: assessment-type
        value: continuous
    observations:
      - uuid: scan-observation-uuid
        methods:
          - TEST
        types:
          - finding
        collected: "2024-03-15T02:00:00Z"
        description: "Automated vulnerability scan results"
```

## Validation

```bash
# Validate assessment results
oscal-cli validate assessment-results.json
```

## Official Examples

| Example | Location |
|---------|----------|
| Sample Assessment Results | [GitHub Directory](https://github.com/usnistgov/oscal-content/tree/main/examples/ar) |
| FedRAMP SAR Templates | [GitHub Repository](https://github.com/GSA/fedramp-automation/tree/master/dist/content/rev5/templates) |

## Resources

- [OSCAL Assessment Results Reference](https://pages.nist.gov/OSCAL/concepts/layer/assessment/assessment-results/)
- [FedRAMP Automation Templates](https://github.com/GSA/fedramp-automation)

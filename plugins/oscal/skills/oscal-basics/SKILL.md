---
name: oscal-basics
description: Use when working with OSCAL documents, security controls, or compliance artifacts - routes to specific model skills
---

# OSCAL Basics

OSCAL (Open Security Controls Assessment Language) is a set of standardized, machine-readable formats for security documentation developed by NIST.

**For detailed model documentation, see the specific skills below.**

## OSCAL Layers and Models

### Control Layer

Defines security controls and baselines.

| Model | Skill | Description |
|-------|-------|-------------|
| **Catalog** | `oscal-catalog` | Organizes security controls with statements, parameters, and guidance |
| **Profile** | `oscal-profile` | Selects and tailors controls to create baselines (e.g., FedRAMP Moderate) |
| **Mapping** | `oscal-mapping` | Defines relationships between controls in different frameworks |

### Implementation Layer

Documents how controls are implemented.

| Model | Skill | Description |
|-------|-------|-------------|
| **Component Definition** | `oscal-component-definition` | Describes control implementations for reusable components |
| **System Security Plan (SSP)** | `oscal-ssp` | Documents a system's security implementation against a baseline |

### Assessment Layer

Covers evaluation and remediation.

| Model | Skill | Description |
|-------|-------|-------------|
| **Assessment Plan (SAP)** | `oscal-assessment-plan` | Plans assessment scope, methods, and schedule |
| **Assessment Results (SAR)** | `oscal-assessment-results` | Documents findings, observations, and risks |
| **POA&M** | `oscal-poam` | Tracks remediation of identified weaknesses |

## Data Formats

OSCAL supports three equivalent formats:

| Format | Extension | Use Case |
|--------|-----------|----------|
| JSON | `.json` | APIs, programmatic processing |
| XML | `.xml` | Legacy systems, XSLT transforms |
| YAML | `.yaml` | Human editing, configuration |

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Control** | A security requirement or safeguard |
| **Parameter** | A configurable value within a control |
| **Part** | A component of a control (statement, guidance, objective) |
| **Property** | Metadata about an OSCAL object |
| **Link** | Reference to related resources |
| **Back Matter** | Resources and references at document end |

## Common Workflows

### Creating a Baseline

1. Start with a catalog (`oscal-catalog`)
2. Create a profile to select/tailor controls (`oscal-profile`)
3. Resolve profile to produce final catalog

### Documenting a System

1. Import baseline profile
2. Create SSP with system characteristics (`oscal-ssp`)
3. Document control implementations
4. Reference component definitions (`oscal-component-definition`)

### Conducting Assessment

1. Create assessment plan from SSP (`oscal-assessment-plan`)
2. Execute assessment activities
3. Document results and findings (`oscal-assessment-results`)
4. Track remediation in POA&M (`oscal-poam`)

### Multi-Framework Compliance

1. Map controls between frameworks (`oscal-mapping`)
2. Identify gaps and overlaps
3. Document single implementation satisfying multiple standards

## Validation

Use oscal-cli to validate OSCAL documents:

```bash
oscal-cli validate <document>
oscal-cli validate ssp.json --profile baseline.json
oscal-cli resolve-profile profile.json -o resolved.json
```

→ **For CLI usage:** See `using-oscal-cli` skill (from `oscal-tools` plugin)

## Quick Reference

| Task | Skill |
|------|-------|
| Define security controls | `oscal-catalog` |
| Create security baseline | `oscal-profile` |
| Map between frameworks | `oscal-mapping` |
| Document component capabilities | `oscal-component-definition` |
| Write system security plan | `oscal-ssp` |
| Plan an assessment | `oscal-assessment-plan` |
| Document assessment findings | `oscal-assessment-results` |
| Track remediation | `oscal-poam` |
| Use oscal-cli | `using-oscal-cli` (oscal-tools plugin) |

## Resources

- [NIST OSCAL Homepage](https://pages.nist.gov/OSCAL/)
- [OSCAL GitHub Repository](https://github.com/usnistgov/OSCAL)
- [FedRAMP Automation](https://github.com/GSA/fedramp-automation)

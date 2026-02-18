---
name: oscal-basics
description: Use when working with OSCAL documents, security controls, or compliance artifacts - routes to specific model skills
---

# OSCAL Basics

OSCAL (Open Security Controls Assessment Language) is a set of standardized, machine-readable formats for security documentation developed by NIST.

**For detailed model documentation, see the specific skills below.**

## Iron Rules

These rules MUST always be followed when creating or editing OSCAL content.

- **Use random UUIDs.** When creating UUIDs, always generate random (v4) UUIDs. Never use predictable, sequential, or deterministic UUIDs.
- **Update the document-level UUID on every change.** When creating or updating OSCAL content, set the top-level `uuid` field to a new random UUID. This signals that the document has changed and prevents stale references.
- **Never change system IDs.** The `system-id` fields are intended to be stable, unchanging identifiers. Do not regenerate or modify them.

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

## OSCAL Release History

| Version | Date | Notes |
|---------|------|-------|
| v1.2.0 | 2025-12-12 | Latest stable release |
| v1.1.3 | 2024-11-26 | Patch release |
| v1.1.2 | 2023-12-06 | Patch release |
| v1.1.1 | 2023-09-12 | Patch release |
| v1.1.0 | 2023-07-25 | Minor release |
| v1.0.6 | 2023-06-30 | Patch release |
| v1.0.5 | 2023-04-18 | Patch release |
| v1.0.4 | 2022-05-16 | Patch release |
| v1.0.3 | 2022-05-09 | Patch release |
| v1.0.2 | 2022-03-20 | Patch release |
| v1.0.1 | 2022-01-30 | Patch release |
| v1.0.0 | 2021-06-08 | First stable release |
| v1.0.0-rc2 | 2021-04-12 | Release candidate 2 |
| v1.0.0-rc1 | 2020-12-21 | Release candidate 1 |
| v1.0.0-milestone3 | 2020-06-03 | Pre-release milestone |
| v1.0.0-milestone2 | 2019-10-01 | Pre-release milestone |
| v1.0.0-milestone1 | 2019-06-16 | Pre-release milestone |

## Resources

- [NIST OSCAL Homepage](https://pages.nist.gov/OSCAL/)
- [OSCAL GitHub Repository](https://github.com/usnistgov/OSCAL)
- [OSCAL Releases](https://github.com/usnistgov/OSCAL/releases)
- [FedRAMP Automation](https://github.com/GSA/fedramp-automation)

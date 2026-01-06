---
name: oscal-basics
description: Use when working with OSCAL documents, security controls, or compliance artifacts
---

# OSCAL Basics

OSCAL (Open Security Controls Assessment Language) is a set of standardized formats for security documentation.

## OSCAL Layers

### Control Layer
- **Catalog**: Defines security controls (e.g., NIST SP 800-53)
- **Profile**: Selects and tailors controls for a baseline (e.g., FedRAMP High)

### Implementation Layer
- **Component Definition**: Describes how components implement controls
- **System Security Plan (SSP)**: Documents system security implementation

### Assessment Layer
- **Security Assessment Plan (SAP)**: Plans assessment activities
- **Security Assessment Results (SAR)**: Documents assessment findings
- **Plan of Action and Milestones (POA&M)**: Tracks remediation

### Mapping Layer
- **Mapping**: Defines relationships between controls in different catalogs
- Enables crosswalks between frameworks (e.g., NIST 800-53 to ISO 27001)
- Supports control equivalence and gap analysis

## Data Formats

OSCAL supports three equivalent formats:
- JSON (`.json`)
- XML (`.xml`)
- YAML (`.yaml`)

## Key Concepts

- **Control**: A security requirement or safeguard
- **Parameter**: A configurable value within a control
- **Part**: A component of a control (statement, guidance, etc.)
- **Property**: Metadata about an OSCAL object
- **Link**: Reference to related resources
- **Mapping**: Relationship between source and target controls

## Mapping Model

The OSCAL Mapping model enables:
- Defining relationships between controls across frameworks
- Specifying relationship types (equivalent, subset, superset, etc.)
- Documenting rationale for mappings
- Supporting compliance crosswalks and gap analysis

When helping with OSCAL tasks, consider:
1. Which OSCAL model is appropriate
2. The compliance framework being used
3. Data format preferences
4. Validation requirements
5. Cross-framework mapping needs

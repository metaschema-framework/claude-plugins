# OSCAL Plugin

Skills and agents for understanding [OSCAL](https://pages.nist.gov/OSCAL/) (Open Security Controls Assessment Language) security models.

## Installation

```bash
claude plugin install metaschema-framework:oscal
```

## Features

### Skills

- Understanding OSCAL layers:
  - Catalog - Security control definitions
  - Profile - Control baselines and tailoring
  - Component Definition - System component capabilities
  - System Security Plan (SSP) - Security implementation
  - Security Assessment Plan (SAP)
  - Security Assessment Results (SAR)
  - Plan of Action and Milestones (POA&M)
- Working with OSCAL data formats (JSON, XML, YAML)
- Security compliance concepts

### Agents

- OSCAL/security compliance expert for answering questions

## Usage

Once installed, Claude will automatically use these skills when working with OSCAL-related tasks.

## Related

- [oscal-tools](../oscal-tools) - CLI tooling integration
- [OSCAL Documentation](https://pages.nist.gov/OSCAL/)
- [FedRAMP Automation](https://automate.fedramp.gov/)

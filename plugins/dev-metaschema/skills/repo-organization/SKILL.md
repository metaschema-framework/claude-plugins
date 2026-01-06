---
name: repo-organization
description: Use when working in metaschema-framework repositories to understand project structure and relationships
---

# Metaschema Framework Repository Organization

The metaschema-framework GitHub organization contains interconnected repositories for the Metaschema information modeling framework and OSCAL tooling.

## Repository Overview

### Specification
- **metaschema** - The Metaschema specification and documentation
  - Defines the modeling language syntax and semantics
  - Contains specification documents and examples

### Build Infrastructure
- **oss-maven** - Parent POM and shared build configuration
  - Defines common Maven settings for all Java projects
  - Plugin configurations, dependency management, release profiles
  - All Java projects inherit from this parent POM
- **maven2** - Maven repository for published releases
  - Hosts released artifacts for public consumption
  - Used as a Maven repository via GitHub Pages

### Core Java Libraries
- **metaschema-java** - Java implementation of Metaschema
  - Core data binding framework
  - XML/JSON parsing and serialization
  - Constraint validation engine
  - Schema generation (XML Schema, JSON Schema)
  - Depends on: oss-maven (parent)

- **liboscal-java** - OSCAL Java bindings
  - Generated Java classes for OSCAL models
  - Built using metaschema-java
  - Depends on: oss-maven (parent), metaschema-java

### Command-Line Tools
- **oscal-cli** - OSCAL command-line tool
  - Validation, conversion, profile resolution
  - Built on liboscal-java and metaschema-java
  - Depends on: oss-maven (parent), liboscal-java, metaschema-java

## Dependency Hierarchy

```
oss-maven (parent POM)
    └── metaschema-java
            └── liboscal-java
                    └── oscal-cli
```

## Common Patterns

### Build System
- All Java projects use Maven
- Parent POM in oss-maven provides shared configuration
- Multi-module Maven projects are common
- GitHub Actions for CI/CD

### Project Structure (Java repos)
```
repo/
├── pom.xml                 # Parent POM (inherits oss-maven)
├── module-core/
│   ├── pom.xml
│   └── src/
├── module-cli/
│   ├── pom.xml
│   └── src/
└── ...
```

### Versioning
- Semantic versioning (MAJOR.MINOR.PATCH)
- SNAPSHOT versions for development
- Releases published to maven2 repository

## Working Across Repositories

When making changes that span repositories:
1. Start with the lowest dependency (e.g., metaschema-java)
2. Release or install SNAPSHOT locally
3. Update dependent projects to use new version
4. Test integration before releasing

For local development across repos:
```bash
# Install SNAPSHOT to local Maven repo
mvn install -DskipTests

# Other projects can then use the SNAPSHOT version
```

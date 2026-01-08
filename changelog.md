# Changelog Writing Reference Guide

## Fundamental Principles

A changelog constitutes a chronologically ordered record of notable modifications made to a software project. The primary objective is to facilitate user comprehension of alterations between successive releases.

## Structural Organization

### Hierarchical Format

Changelogs should adhere to a hierarchical structure with releases ordered in reverse chronological sequence (most recent first). Each release entry must include:

- Version identifier (semantic versioning recommended)
- Release date in ISO 8601 format (YYYY-MM-DD)
- Categorized list of modifications

### Standard Classification Schema

Changes should be systematically categorized using the following taxonomy:

- **Added**: New features or functionality introduced to the system
- **Changed**: Modifications to existing functionality or behavior
- **Deprecated**: Features marked for future removal; maintained for backward compatibility
- **Removed**: Features or functionality eliminated from the codebase
- **Fixed**: Corrections to defects or erroneous behavior
- **Security**: Patches addressing security vulnerabilities or concerns

## Compositional Guidelines

### Content Specifications

Each entry should satisfy the following criteria:

1. **User-Centric Perspective**: Document changes from the end-user viewpoint rather than implementation details
2. **Specificity**: Provide precise descriptions of modifications, avoiding vague terminology
3. **Conciseness**: Maintain brevity while ensuring comprehensiveness
4. **Actionability**: Include sufficient information for users to assess impact on their workflows

### Linguistic Conventions

- Employ consistent grammatical tense throughout the document
- Initiate entries with action verbs (past or present tense, uniformly applied)
- Maintain parallel grammatical structure across entries
- Utilize imperative or declarative voice as appropriate

### Inclusion Criteria

Document modifications that:
- Affect user-facing functionality or interface
- Introduce breaking changes requiring user action
- Resolve reported defects
- Enhance performance metrics
- Address security vulnerabilities

Exclude modifications that:
- Constitute internal refactoring without behavioral changes
- Involve non-user-facing infrastructure updates
- Pertain solely to development or testing processes

## Technical Conventions

### Version Numbering

Adhere to Semantic Versioning (SemVer) principles:
- MAJOR version for incompatible API changes
- MINOR version for backward-compatible functionality additions
- PATCH version for backward-compatible defect corrections

### Cross-Referencing

Incorporate references to:
- Issue tracking system identifiers (e.g., #1234)
- Pull request or merge request numbers
- External documentation when applicable

## Best Practices

1. Maintain changelog currency concurrent with development activities
2. Emphasize breaking changes through typographical distinction or dedicated sections
3. Provide migration guidance for deprecated or removed features
4. Ensure accessibility and discoverability of the changelog document
5. Validate entries for accuracy and completeness prior to release publication

## Example Template

```
## [Version X.Y.Z] - YYYY-MM-DD

### Added
- Feature description with user impact specification

### Changed
- Modification description with rationale if applicable

### Deprecated
- Feature identification with timeline for removal

### Removed
- Eliminated feature with migration path if relevant

### Fixed
- Defect description with issue reference (#123)

### Security
- Vulnerability description (consider detail level for public disclosure)
```

---

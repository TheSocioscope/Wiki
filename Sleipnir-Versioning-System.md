# Sleipnir - Versioning System

This document details the versioning system used for prompting sheets in Sleipnir.

## Overview

The Sleipnir versioning system ensures reproducibility, traceability, and effective management of prompt evolution over time. Proper versioning is critical for research consistency and collaborative development.

## Version Number Format

Sleipnir uses **Semantic Versioning** (SemVer) for prompting sheets:

```
MAJOR.MINOR.PATCH
```

### Version Components

- **MAJOR** - Incompatible changes that significantly alter prompt behavior or output format
- **MINOR** - Backward-compatible functionality additions or improvements
- **PATCH** - Backward-compatible bug fixes and minor clarifications

### Examples

- `1.0.0` - Initial stable release
- `1.1.0` - Added new parameter options (backward compatible)
- `1.1.1` - Fixed typo in prompt text
- `2.0.0` - Complete prompt restructure (breaking change)

## Version Metadata

Each prompt version includes metadata:

```yaml
version: 1.2.0
created: 2025-10-15T10:30:00Z
author: researcher@example.com
description: Added temperature parameter control
changelog: |
  - Added temperature parameter
  - Improved instruction clarity
  - Fixed formatting issues
parent_version: 1.1.0
tags:
  - analysis
  - sentiment
status: stable  # draft, experimental, stable, deprecated
```

## Version Lifecycle

### Development Stages

1. **Draft** (`0.x.x`) - Under active development, not for production use
2. **Experimental** (`x.x.x-beta`) - Testing in progress, may have issues
3. **Stable** (`x.x.x`) - Production-ready, thoroughly tested
4. **Deprecated** - Scheduled for removal, use newer version

### Version Transitions

```
Draft (0.1.0) → Experimental (0.9.0-beta) → Stable (1.0.0) → Deprecated
```

## Versioning Best Practices

### When to Increment MAJOR

- Complete prompt rewrite
- Change in expected output format
- Removal of parameters or functionality
- Fundamental approach change

### When to Increment MINOR

- Add new optional parameters
- Enhance instructions without changing core behavior
- Add new examples
- Improve prompt clarity

### When to Increment PATCH

- Fix typos or grammatical errors
- Minor formatting adjustments
- Update outdated references
- Small clarifications

## Version Control in Git

### File Naming Convention

Store prompts with version information:

```
prompts/
├── sentiment_analysis/
│   ├── v1.0.0.txt
│   ├── v1.1.0.txt
│   ├── v2.0.0.txt
│   └── current.txt -> v2.0.0.txt
```

### Git Tagging

Tag significant prompt versions:

```bash
git tag -a prompt-sentiment-v2.0.0 -m "Major update to sentiment analysis prompt"
git push origin prompt-sentiment-v2.0.0
```

### Branching Strategy

- `main` - Stable prompt versions only
- `develop` - Active development
- `feature/prompt-name` - Experimental prompt development
- `hotfix/prompt-name` - Critical fixes for stable prompts

## Version Resolution

### Specifying Versions in Jobs

```yaml
job:
  name: analysis_job
  prompts:
    - name: sentiment_analysis
      version: 2.0.0          # Exact version
    - name: topic_extraction
      version: ^1.2.0         # Compatible with 1.2.0 or higher 1.x
    - name: summarization
      version: ~1.2.3         # Compatible with 1.2.x patches
    - name: classification
      version: latest         # Use latest stable version (not recommended for production)
```

### Version Resolution Rules

1. Exact version (`2.0.0`) - Use specified version exactly
2. Caret (`^1.2.0`) - Compatible changes, same major version
3. Tilde (`~1.2.3`) - Patch-level changes only
4. `latest` - Latest stable version (avoid in production)

## Version Compatibility Matrix

Track compatibility between prompts and models:

| Prompt Version | GPT-4 | Claude 3 | GPT-3.5 | Status |
|----------------|-------|----------|---------|--------|
| 1.0.0 | ✓ | ✓ | ✓ | Deprecated |
| 2.0.0 | ✓ | ✓ | ✗ | Stable |
| 2.1.0 | ✓ | ✓ | ✗ | Stable |
| 3.0.0-beta | ✓ | ✗ | ✗ | Experimental |

## Version Documentation

### Changelog Format

Maintain a `CHANGELOG.md` for each prompt:

```markdown
# Changelog

## [2.0.0] - 2025-10-20

### Changed
- Restructured prompt for improved clarity
- Modified output format to JSON

### Added
- Support for multi-language analysis
- Temperature parameter control

### Removed
- Legacy format_style parameter

## [1.1.0] - 2025-09-15

### Added
- Examples for edge cases
- Better handling of ambiguous inputs

### Fixed
- Typo in instruction text
```

### Migration Guides

Provide migration guidance for major version changes:

```markdown
# Migration Guide: v1.x to v2.0

## Breaking Changes

1. Output format changed from text to JSON
2. `format_style` parameter removed

## How to Migrate

- Update job configurations to parse JSON output
- Remove `format_style` parameter from job configs
- Test with sample data before production deployment
```

## Version Archival

### Retention Policy

- **Stable versions** - Retain indefinitely
- **Experimental versions** - Retain for 6 months after superseded
- **Deprecated versions** - Retain for 1 year after deprecation

### Archive Storage

```
archives/
└── prompts/
    └── sentiment_analysis/
        └── deprecated/
            ├── v0.9.0.txt
            └── v1.0.0.txt
```

## Automated Version Management

### Version Bumping

Use tooling to automate version increments:

```bash
sleipnir prompt bump-version sentiment_analysis --type minor
sleipnir prompt bump-version sentiment_analysis --type major
```

### Version Validation

Validate version consistency:

```bash
sleipnir prompt validate-versions
sleipnir prompt check-compatibility --job my_job
```

## Reproducibility

### Version Pinning

For research reproducibility, always pin exact versions:

```yaml
research_experiment:
  prompt_versions:
    sentiment: 2.1.0
    classification: 1.3.2
    summarization: 3.0.1
  locked: true
```

### Version Lock Files

Generate lock files for experiments:

```bash
sleipnir generate-lockfile --job research_experiment
```

## Related Documentation

- [Sleipnir Creating a Module](Sleipnir-Creating-a-Module.md)
- [Sleipnir Prompting Stack](Sleipnir-Prompting-Stack.md)
- [Sleipnir Jobs](Sleipnir-Jobs.md)
- [Glossary](Glossary.md)

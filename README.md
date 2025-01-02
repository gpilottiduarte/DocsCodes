# Documentation Readability Analyzer

## Overview

The Documentation Readability Analyzer is a GitHub Actions workflow that automatically assesses the readability of Markdown documentation files within a repository. The analyzer generates comprehensive metrics and statistics for documentation quality assessment.

## Technical Architecture

### Core Components

The system comprises three primary components:

1. GitHub Actions Workflow Configuration
2. Python-based Analysis Engine
3. Automated Report Generation System

### Dependencies

### Core Dependencies
- Python 3.10
- textstat >= 0.7.3
- pandas >= 2.0.0

### Documentation Linting
- Vale Linter
  - Configuration Path: `.github/styles`
  - Minimum Alert Level: suggestion
  - Style Implementation: Vale standard style

## Implementation Details

### Custom Vale Rules
The workflow includes custom Vale linting rules specifically designed for documentation quality control:

#### Rule Structure
```yaml
StylesPath = styles
MinAlertLevel = suggestion

[*.{md,mdx}]
BasedOnStyles = senhasegura

[senhasegura]
alert = error
warning = warning
info = suggestion
```

### Custom Vocabulary
- `accept.txt`: Approved terminology list
- `reject.txt`: Prohibited terminology list

### Style Rules Implementation
Pattern Validation:

```yml
extends: regex
message: "Titles must follow pattern: number_language_type_name"
level: error
scope: text
pattern: '\d{2}_(PT|EN)_(REF|EXP|HOW)_[A-Za-z]+'
```

### Product-Specific Rules
```yml
extends: substitution
message: "Use correct module name: '%s'"
level: error
ignorecase: false
swap:
  "devops manager": "DevOps Secret Manager"
  "password manager": "Password Safe"
```

### Interface Elements
```yml
extends: existence
message: "Use bold (**) for interface elements"
level: warning
scope: text
tokens:
  - "(?<!\\*\\*)(Save|Cancel|Edit|Delete)(?!\\*\\*)"
```

### Rule Categories
- Terminology validation
- Document structure
- Formatting requirements
- Interface descriptions
- Mandatory fields
- Navigation paths
- Status indicators
- Procedure formatting

### Implementation Files
- Required files structure:

```
.
├── .vale.ini
└── styles/
    └── senhasegura/
        ├── accept.txt
        ├── reject.txt
        ├── patterns.yml
        ├── senhasegura_rules.yml
        └── formatting.yml
```

### Validation Levels
- error: Critical documentation issues
- warning: Style recommendations
- suggestion: Optional improvements

**This custom Vale implementation ensures:**
- Consistent terminology usage
- Standardized documentation structure
- Proper interface element description
- Correct formatting practices
- Navigation clarity

## Workflow Triggers

The analyzer activates on:
- Push events to main/master branches (*.md, *.mdx files)
- Pull request events to main/master branches (*.md, *.mdx files)
- Manual workflow dispatch

## Analysis Process

The ReadabilityAnalyzer class performs the following operations:

1. File Discovery
   - Recursively identifies all Markdown files
   - Excludes standard files (CHANGELOG.md, LICENSE.md, etc.)

2. Content Analysis
   - Calculates readability metrics per file:
     - Flesch Reading Ease
     - Flesch-Kincaid Grade Level
     - Gunning Fog Index
     - SMOG Index
     - Coleman-Liau Index
     - Automated Readability Index
     - Dale-Chall Score

3. Statistical Analysis
   - Generates document statistics:
     - Word count
     - Sentence count
     - Syllable count
     - Average words per sentence
     - Average syllables per word
     - Estimated reading time

## Output Format

### JSON Report Structure

```json
{
    "timestamp": "ISO-8601 timestamp",
    "summary": {
        "total_files": "integer",
        "configuration": {
            "reading_speed_wpm": "float"
        }
    },
    "results": [
        {
            "filepath": "string",
            "metrics": {
                "flesch_reading_ease": "float",
                ...
            },
            "statistics": {
                "word_count": "float",
                ...
            }
        }
    ]
}
```

### Pull Request Integration

For pull requests, the analyzer automatically generates a formatted comment containing:
- Analysis summary
- Per-file metrics tables
- Document statistics
- Reading time estimates

## Configuration

The analyzer accepts the following environment variables:
- READING_SPEED_WPM: Default 200 (words per minute)

## Output Location

Reports are stored in:
- Detailed JSON: `readability_report/detailed_report.json`
- GitHub Actions Artifacts: Uploaded as 'readability-report'

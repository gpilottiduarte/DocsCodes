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

### Vale Configuration

The workflow incorporates Vale linting with the following configuration:

```ini
StylesPath = .github/styles
MinAlertLevel = suggestion
[*.md]
BasedOnStyles = Vale
```

This configuration ensures:
- Standardized style checking
- Consistent documentation quality
- Automated style enforcement
- Minimum suggestion-level alerts

### Workflow Triggers

The analyzer activates on:
- Push events to main/master branches (*.md, *.mdx files)
- Pull request events to main/master branches (*.md, *.mdx files)
- Manual workflow dispatch

### Analysis Process

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

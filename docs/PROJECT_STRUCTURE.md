# Project Structure

This document describes the organisation of the TF-Analyst Pro repository.

## Directory Structure

```
tf-analyst-pro/
├── assets/
│   └── images/
│       ├── logo.png                  # Main logo
├── src/
│   ├── tf-analyst-pro.pine           # Main script file
├── docs/
│   ├── algorithm.md                  # Algorithum
│   └── PROJECT_STRUCTURE.md          # This file
├── LICENSE                           # MIT License
└── README.md                         # Main documentation
```

## Directory Purposes

### `/assets`
Contains all static assets used in the project:
- `images/`: Logos and other image assets in various sizes
- Additional resources like screenshots or diagrams (future)

### `/src`
Contains all source code files:
- Main TradingView Pine Script files
- Any additional helper scripts or variations

### `/docs`
Project documentation:
- Technical documentation
- Implementation guides
- Configuration references
- Future development plans

## File Naming Conventions

1. Use lowercase with hyphens for source files
2. Use uppercase with underscores for documentation
3. Use descriptive suffixes for variations (e.g., -singles for single symbol version)
4. Include size in filename for resolution-specific assets

## Versioning

The project follows semantic versioning:
- Major version: Breaking changes
- Minor version: New features
- Patch version: Bug fixes

Current version: 1.2.0

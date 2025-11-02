# Getting Started

This guide will help you get started with the Devin AI PoC documentation site.

## Prerequisites

To work with this documentation site, you'll need:

- Python 3.9 or higher
- pip (Python package installer)
- Git

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/millsks/devin-ai-poc.git
cd devin-ai-poc
```

### 2. Install Documentation Dependencies

Install the required Python packages for building the documentation:

```bash
pip install -e ".[docs]"
```

Alternatively, you can install the dependencies directly:

```bash
pip install mkdocs mkdocs-material pymdown-extensions
```

## Building the Documentation

### Local Development Server

To preview the documentation locally with live reloading:

```bash
mkdocs serve
```

This will start a local development server at `http://127.0.0.1:8000/`. The documentation will automatically rebuild when you make changes to the markdown files.

### Building Static Files

To build the documentation as static HTML files:

```bash
mkdocs build
```

The generated files will be in the `site/` directory.

## Documentation Structure

The documentation follows this structure:

```
devin-ai-poc/
├── docs/                  # Documentation source files
│   ├── index.md          # Home page
│   ├── getting-started.md # Getting started guide
│   ├── user-guide/       # User guide section
│   │   ├── overview.md
│   │   └── configuration.md
│   ├── contributing.md   # Contributing guidelines
│   └── about.md          # About page
├── mkdocs.yml            # MkDocs configuration
├── pyproject.toml        # Python project configuration
└── site/                 # Generated site (gitignored)
```

## Writing Documentation

Documentation is written in Markdown with support for extended features:

### Code Blocks

````markdown
```python
def hello_world():
    print("Hello, World!")
```
````

### Admonitions

```markdown
!!! note
    This is a note admonition.

!!! warning
    This is a warning admonition.
```

### Tables

```markdown
| Column 1 | Column 2 |
|----------|----------|
| Value 1  | Value 2  |
```

## Next Steps

- Learn more in the [User Guide](user-guide/overview.md)
- Understand how to [configure](user-guide/configuration.md) the documentation site
- Read about [contributing](contributing.md) to the documentation

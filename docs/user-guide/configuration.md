# Configuration

Learn how to configure and customize this documentation site.

## MkDocs Configuration

The main configuration file is `mkdocs.yml` in the repository root. This file controls all aspects of the documentation site.

### Basic Settings

```yaml
site_name: Devin AI PoC Documentation
site_description: Documentation for Devin AI Proof of Concept
site_author: millsks
repo_name: millsks/devin-ai-poc
repo_url: https://github.com/millsks/devin-ai-poc
```

### Theme Configuration

The Material theme can be customized extensively:

```yaml
theme:
  name: material
  palette:
    - scheme: default      # Light mode
      primary: indigo
      accent: indigo
    - scheme: slate        # Dark mode
      primary: indigo
      accent: indigo
```

#### Available Color Options

Primary and accent colors can be set to:

- `red`, `pink`, `purple`, `deep-purple`, `indigo`, `blue`
- `light-blue`, `cyan`, `teal`, `green`, `light-green`
- `lime`, `yellow`, `amber`, `orange`, `deep-orange`

### Navigation Structure

Control the navigation menu in `mkdocs.yml`:

```yaml
nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - User Guide:
      - Overview: user-guide/overview.md
      - Configuration: user-guide/configuration.md
  - Contributing: contributing.md
  - About: about.md
```

### Markdown Extensions

Enable additional Markdown features:

```yaml
markdown_extensions:
  - pymdownx.highlight      # Code highlighting
  - pymdownx.superfences    # Nested code blocks
  - pymdownx.details        # Collapsible sections
  - pymdownx.tabbed         # Tabbed content
  - admonition              # Callout blocks
  - tables                  # Table support
  - toc:                    # Table of contents
      permalink: true       # Add permalinks to headings
```

## Python Dependencies

Project dependencies are managed in `pyproject.toml`:

```toml
[project.optional-dependencies]
docs = [
    "mkdocs>=1.5.3",
    "mkdocs-material>=9.5.0",
    "mkdocs-material-extensions>=1.3.1",
    "pymdown-extensions>=10.7",
]
```

To install documentation dependencies:

```bash
pip install -e ".[docs]"
```

## GitHub Pages Deployment

The documentation is configured to be deployed to GitHub Pages:

```yaml
site_url: https://millsks.github.io/devin-ai-poc/
```

### Manual Deployment

To deploy manually:

```bash
mkdocs gh-deploy
```

This command:
1. Builds the documentation
2. Pushes the built site to the `gh-pages` branch
3. Makes it available at the configured site URL

### Automated Deployment

For automated deployment, use GitHub Actions (see the `.github/workflows/deploy-docs.yml` file).

## Advanced Customization

### Custom CSS

Add custom styles by creating `docs/stylesheets/extra.css` and referencing it:

```yaml
extra_css:
  - stylesheets/extra.css
```

### Custom JavaScript

Add custom scripts by creating `docs/javascripts/extra.js` and referencing it:

```yaml
extra_javascript:
  - javascripts/extra.js
```

### Plugins

Add additional plugins:

```yaml
plugins:
  - search               # Search functionality
  - tags                 # Tag support
  - git-revision-date    # Last updated dates
```

## Environment Variables

For sensitive configuration, use environment variables:

```bash
export GOOGLE_ANALYTICS_ID="UA-XXXXXXXX-X"
```

Then reference in `mkdocs.yml`:

```yaml
extra:
  analytics:
    provider: google
    property: !ENV GOOGLE_ANALYTICS_ID
```

## Next Steps

- Return to the [User Guide Overview](overview.md)
- Learn about [contributing](../contributing.md) to the documentation
- Explore the [MkDocs documentation](https://www.mkdocs.org/)
- Check out [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)

# Devin AI PoC Documentation

This repository contains a documentation site built with [MkDocs](https://www.mkdocs.org/) and the [Material theme](https://squidfunk.github.io/mkdocs-material/), following best practices for Python project documentation.

## Features

- 📝 **Modern Documentation**: Built with MkDocs and Material theme
- 🚀 **GitHub Pages Ready**: Configured for easy deployment to GitHub Pages
- 🎨 **Beautiful Design**: Responsive design with dark mode support
- 🔍 **Full-Text Search**: Fast and accurate search functionality
- 📱 **Mobile Friendly**: Works seamlessly on all devices
- 🔧 **Extensible**: Easy to customize and extend

## Quick Start

### Prerequisites

- Python 3.9 or higher
- pip (Python package installer)

### Installation

1. Clone the repository:

```bash
git clone https://github.com/millsks/devin-ai-poc.git
cd devin-ai-poc
```

2. Install dependencies:

```bash
pip install -e ".[docs]"
```

### Local Development

Start the development server with live reloading:

```bash
mkdocs serve
```

Visit `http://127.0.0.1:8000/` to view the documentation.

### Building

Build the documentation as static HTML:

```bash
mkdocs build
```

The generated files will be in the `site/` directory.

## Documentation Structure

```
devin-ai-poc/
├── docs/                  # Documentation source files (Markdown)
│   ├── index.md          # Home page
│   ├── getting-started.md # Getting started guide
│   ├── user-guide/       # User guide section
│   │   ├── overview.md
│   │   └── configuration.md
│   ├── contributing.md   # Contributing guidelines
│   └── about.md          # About page
├── .github/
│   └── workflows/
│       └── deploy-docs.yml # GitHub Actions workflow for deployment
├── mkdocs.yml            # MkDocs configuration file
├── pyproject.toml        # Python project configuration
└── site/                 # Generated static site (gitignored)
```

## GitHub Pages Deployment

### Automatic Deployment

This repository is configured with GitHub Actions to automatically deploy to GitHub Pages when changes are pushed to the `main` branch.

The documentation will be available at: https://millsks.github.io/devin-ai-poc/

### Manual Deployment

You can also deploy manually using:

```bash
mkdocs gh-deploy
```

This command builds the documentation and pushes it to the `gh-pages` branch.

### Setting Up GitHub Pages

1. Go to your repository settings
2. Navigate to "Pages" in the left sidebar
3. Under "Build and deployment":
   - Source: Select "GitHub Actions"
4. The workflow will automatically deploy on the next push to `main`

## Contributing

Contributions are welcome! Please see the [Contributing Guide](docs/contributing.md) for details.

## Technology Stack

- **[MkDocs](https://www.mkdocs.org/)**: Fast, simple static site generator
- **[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)**: Modern, feature-rich theme
- **[PyMdown Extensions](https://facelessuser.github.io/pymdown-extensions/)**: Extended Markdown features
- **[GitHub Actions](https://github.com/features/actions)**: Automated deployment
- **[GitHub Pages](https://pages.github.com/)**: Free hosting

## Resources

- [View Documentation](https://millsks.github.io/devin-ai-poc/)
- [MkDocs Documentation](https://www.mkdocs.org/)
- [Material Theme Documentation](https://squidfunk.github.io/mkdocs-material/)
- [Markdown Guide](https://www.markdownguide.org/)

## License

This project is open source and available under the MIT License.
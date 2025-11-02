# Contributing

Thank you for your interest in contributing to the Devin AI PoC documentation! This guide will help you get started.

## How to Contribute

There are many ways to contribute:

- **Improve Documentation**: Fix typos, clarify explanations, add examples
- **Report Issues**: Report problems or suggest improvements
- **Add Content**: Contribute new guides or tutorials
- **Review Changes**: Provide feedback on pull requests

## Getting Started

### 1. Fork and Clone

1. Fork the repository on GitHub
2. Clone your fork locally:

```bash
git clone https://github.com/YOUR-USERNAME/devin-ai-poc.git
cd devin-ai-poc
```

### 2. Set Up Development Environment

Install documentation dependencies:

```bash
pip install -e ".[docs]"
```

### 3. Create a Branch

Create a new branch for your changes:

```bash
git checkout -b feature/improve-getting-started-guide
```

Use descriptive branch names:
- `feature/` for new content
- `fix/` for corrections
- `docs/` for documentation improvements

## Making Changes

### Writing Documentation

1. Start the development server:

```bash
mkdocs serve
```

2. Edit the Markdown files in the `docs/` directory
3. Preview changes at `http://127.0.0.1:8000/`
4. The site will automatically reload when you save changes

### Documentation Style Guide

Follow these guidelines for consistency:

#### Headings

- Use ATX-style headings (`#`, `##`, `###`)
- Use sentence case for headings
- One H1 (`#`) per page (the page title)

#### Code Blocks

Use language-specific code blocks:

````markdown
```python
def example():
    pass
```
````

#### Links

Use descriptive link text:

```markdown
# Good
Learn more about [MkDocs configuration](configuration.md)

# Avoid
Click [here](configuration.md) for more information
```

#### Lists

- Use `-` for unordered lists
- Use `1.` for ordered lists
- Indent nested lists with 2 or 4 spaces

#### Admonitions

Use admonitions for important information:

```markdown
!!! note
    Additional context or information

!!! warning
    Caution or important consideration

!!! tip
    Helpful suggestions or best practices
```

### Testing Your Changes

1. Build the documentation:

```bash
mkdocs build
```

2. Check for broken links and errors in the output
3. Verify the built site in the `site/` directory

## Submitting Changes

### 1. Commit Your Changes

Write clear commit messages:

```bash
git add docs/getting-started.md
git commit -m "Improve installation instructions in getting started guide"
```

#### Commit Message Guidelines

- Use the imperative mood ("Add feature" not "Added feature")
- Keep the first line under 50 characters
- Provide details in the body if needed

### 2. Push to Your Fork

```bash
git push origin feature/improve-getting-started-guide
```

### 3. Create a Pull Request

1. Go to the original repository on GitHub
2. Click "New Pull Request"
3. Select your fork and branch
4. Provide a clear title and description
5. Submit the pull request

### Pull Request Guidelines

- **Title**: Clear and descriptive
- **Description**: Explain what changes were made and why
- **Screenshots**: Include screenshots for visual changes
- **Testing**: Describe how you tested the changes

## Code Review Process

After submitting a pull request:

1. Maintainers will review your changes
2. Address any feedback or requested changes
3. Once approved, your changes will be merged
4. Your contribution will be deployed with the next release

## Questions?

If you have questions:

- Check existing [issues](https://github.com/millsks/devin-ai-poc/issues)
- Open a new issue for discussion
- Reach out to the maintainers

## Thank You!

Your contributions help make this documentation better for everyone. We appreciate your time and effort!

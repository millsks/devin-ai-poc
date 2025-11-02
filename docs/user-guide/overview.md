# User Guide Overview

This section provides detailed information about using and working with the Devin AI PoC documentation.

## What is MkDocs?

MkDocs is a fast, simple, and downright gorgeous static site generator that's geared towards building project documentation. Documentation source files are written in Markdown, and configured with a single YAML configuration file.

## Features

### Material Theme

This documentation uses the Material theme for MkDocs, which provides:

- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices
- **Search**: Fast and accurate search functionality
- **Navigation**: Intuitive navigation with tabs and sections
- **Dark Mode**: Automatic theme switching based on system preferences
- **Code Highlighting**: Beautiful syntax highlighting for code blocks

### Extended Markdown

The documentation supports extended Markdown features through PyMdown Extensions:

#### Admonitions

Admonitions are callout blocks that draw attention to important information:

!!! note "This is a note"
    This is the content of the note.

!!! tip "Pro Tip"
    Use admonitions to highlight important information!

!!! warning "Important"
    Pay attention to warnings to avoid common pitfalls.

!!! danger "Critical"
    Critical information that requires immediate attention.

#### Code Blocks

Code blocks support syntax highlighting and line numbers:

```python linenums="1"
def fibonacci(n):
    """Calculate the nth Fibonacci number."""
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(10))
```

#### Tabs

Content can be organized in tabs:

=== "Python"

    ```python
    print("Hello, World!")
    ```

=== "JavaScript"

    ```javascript
    console.log("Hello, World!");
    ```

=== "Bash"

    ```bash
    echo "Hello, World!"
    ```

### Navigation

The documentation is organized into logical sections:

- **Home**: Overview and introduction
- **Getting Started**: Installation and setup instructions
- **User Guide**: Detailed documentation (you are here!)
- **Contributing**: Guidelines for contributors
- **About**: Project information

## Best Practices

When writing documentation:

1. **Be Clear and Concise**: Use simple language and short sentences
2. **Use Examples**: Include code examples and use cases
3. **Organize Content**: Use headings and sections logically
4. **Link Related Content**: Cross-reference related pages
5. **Keep It Updated**: Regular updates ensure accuracy

## Next Steps

- Learn about [configuration options](configuration.md)
- Read the [contributing guide](../contributing.md)
- Visit the [GitHub repository](https://github.com/millsks/devin-ai-poc)

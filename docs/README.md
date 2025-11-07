# mstools Documentation

This directory contains the documentation for mstools.

## Building the Documentation

### Install Dependencies

```bash
pip install -r requirements.txt
```

Or install all development dependencies:

```bash
pip install -e ".[dev]"
```

### Serve Documentation Locally

```bash
mkdocs serve
```

The documentation will be available at `http://127.0.0.1:8000/`.

### Build Documentation

```bash
mkdocs build
```

This creates a `site/` directory with static HTML files.

## Documentation Structure

```
docs/
├── index.md              # Home page
├── getting-started/      # Getting started guides
│   ├── installation.md   # Installation instructions
│   └── quickstart.md     # Quick start guide
├── guide/                # User guides
│   ├── overview.md       # Overview of mstools
│   ├── ms-files.md       # Working with MS files
│   ├── operations.md     # Operations guide
│   └── export.md         # Export guide
├── api/                  # API reference
│   ├── ms.md             # Ms class documentation
│   ├── data-classes.md   # Data classes
│   ├── operations.md     # Operations functions
│   ├── utilities.md      # Utility functions
│   └── mounts.md         # Mount tools
├── cli/                  # CLI reference
│   └── commands.md       # CLI commands
├── examples.md           # Practical examples
├── contributing.md       # Contributing guide
└── requirements.txt      # Documentation dependencies
```

## Deploying to GitHub Pages

### Option 1: Manual Deployment

```bash
mkdocs gh-deploy
```

This builds and pushes documentation to the `gh-pages` branch.

### Option 2: GitHub Actions (Recommended)

Create `.github/workflows/docs.yml`:

```yaml
name: Build Documentation

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: |
          pip install -r docs/requirements.txt
      
      - name: Build documentation
        run: mkdocs build
      
      - name: Deploy to GitHub Pages
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./site
```

## Updating Documentation

1. Edit markdown files in `docs/`
2. Add new pages to `mkdocs.yml` navigation
3. Use mkdocstrings syntax to auto-generate API docs from docstrings
4. Test locally with `mkdocs serve`
5. Commit and push changes

## MkDocs Configuration

The main configuration is in `mkdocs.yml` at the project root.

Key features enabled:

- **Material theme**: Modern, responsive design
- **mkdocstrings**: Auto-generate API docs from Python docstrings
- **Code highlighting**: Syntax highlighting for code blocks
- **Search**: Full-text search functionality
- **Navigation tabs**: Organized navigation structure

## Writing Documentation

### Code Examples

Use fenced code blocks with language specification:

````markdown
```python
from mstools import Ms
ms = Ms('mydata.ms')
```
````

### API Documentation

Use mkdocstrings to include API docs:

```markdown
::: mstools.tools.msdata.Ms
```

### Admonitions

Use admonitions for notes and warnings:

```markdown
!!! note
    This is a note.

!!! warning
    This is a warning.

!!! tip
    This is a tip.
```

### Cross-References

Link to other pages:

```markdown
See the [Installation Guide](getting-started/installation.md) for details.
```

## Troubleshooting

### Module Not Found

If mkdocstrings can't find the module:

```bash
# Install mstools in development mode
pip install -e .
```

### Build Warnings

Check that all referenced files and modules exist:

```bash
mkdocs build --strict
```

## Resources

- [MkDocs Documentation](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [mkdocstrings](https://mkdocstrings.github.io/)

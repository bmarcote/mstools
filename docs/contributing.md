# Contributing to mstools

Thank you for your interest in contributing to mstools! This guide will help you get started.

## Development Setup

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/mstools.git
cd mstools
```

### 2. Create a Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install in Development Mode

```bash
pip install -e ".[dev]"
```

This installs mstools in editable mode with all development dependencies.

## Code Style

We follow PEP 8 guidelines and use the following tools:

- **ruff**: For linting and formatting
- **mypy**: For type checking

### Running Checks

```bash
# Lint code
ruff check src/

# Format code
ruff format src/

# Type check
mypy src/
```

## Docstring Format

We use Google-style docstrings:

```python
def example_function(param1: str, param2: int) -> bool:
    """Brief description of function.
    
    More detailed description if needed.
    
    Args:
        param1 (str): Description of param1.
        param2 (int): Description of param2.
    
    Returns:
        bool: Description of return value.
    
    Raises:
        ValueError: When this error occurs.
    
    Example:
        >>> example_function("test", 42)
        True
    """
    pass
```

## Testing

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=mstools --cov-report=html

# Run specific test file
pytest tests/test_msdata.py
```

### Writing Tests

Place tests in the `tests/` directory:

```python
import pytest
from mstools import Ms

def test_ms_loading():
    """Test that Ms class loads correctly."""
    ms = Ms('test_data/test.ms')
    assert ms.projectname is not None

def test_antenna_access():
    """Test antenna access methods."""
    ms = Ms('test_data/test.ms')
    assert 'Ef' in ms.antennas
    ef = ms.antennas['Ef']
    assert ef.name == 'Ef'
```

## Documentation

### Building Documentation Locally

```bash
# Install documentation dependencies
pip install -r docs/requirements.txt

# Serve documentation locally
mkdocs serve

# Build documentation
mkdocs build
```

The documentation will be available at `http://127.0.0.1:8000/`.

### Writing Documentation

- Documentation is in `docs/` directory
- Use Markdown format
- Update `mkdocs.yml` when adding new pages
- Include code examples where appropriate

## Pull Request Process

1. **Fork** the repository
2. **Create a branch** for your feature:
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **Make your changes** with clear commit messages
4. **Add tests** for new functionality
5. **Update documentation** if needed
6. **Run checks** (linting, type checking, tests)
7. **Push** to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```
8. **Create a Pull Request** on GitHub

### Pull Request Guidelines

- **Title**: Clear, descriptive title
- **Description**: Explain what and why
- **Tests**: Include tests for new features
- **Documentation**: Update docs if needed
- **Changelog**: Update CHANGELOG.md if appropriate

## Reporting Issues

When reporting bugs, please include:

- **Environment**: OS, Python version, mstools version
- **Expected behavior**: What should happen
- **Actual behavior**: What actually happens
- **Minimal example**: Code to reproduce the issue
- **Error messages**: Full traceback if applicable

Example:

```markdown
**Environment:**
- OS: Ubuntu 22.04
- Python: 3.11.5
- mstools: 1.0.0

**Expected:** `ms.operations.polswap('Ef')` should swap polarizations

**Actual:** Raises `ValueError: Wrong stokes type`

**Code:**
```python
from mstools import Ms
ms = Ms('test.ms')
ms.operations.polswap('Ef')
```

**Error:**
```
ValueError: Wrong stokes type.
  Traceback (most recent call last):
  ...
```
```

## Feature Requests

We welcome feature requests! Please:

1. **Check existing issues** first
2. **Describe the use case** clearly
3. **Provide examples** if possible
4. **Explain the benefit** to other users

## Code of Conduct

- Be respectful and inclusive
- Welcome newcomers
- Focus on constructive feedback
- Assume good intentions

## Questions?

If you have questions:

- **Open an issue** for bugs or feature requests
- **Start a discussion** for general questions
- **Check documentation** first

Thank you for contributing to mstools!

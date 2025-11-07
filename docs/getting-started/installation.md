# Installation

## Requirements

mstools requires Python 3.10 or later and depends on several packages:

- **pyrap/casacore**: For reading Measurement Set files
- **astropy**: For astronomical coordinates and units
- **numpy**: For numerical operations
- **rich**: For beautiful terminal output
- **blessed**: For interactive terminal displays
- **rich-argparse**: For enhanced CLI help

## Install from PyPI

The simplest way to install mstools:

```bash
pip install mstools
```

## Install from Source

To install the latest development version:

```bash
git clone https://github.com/yourusername/mstools.git
cd mstools
pip install -e .
```

## Installing Casacore

The most challenging dependency is typically `python-casacore` (pyrap). Here are platform-specific installation instructions:

### Ubuntu/Debian

```bash
# Install casacore libraries
sudo apt-get update
sudo apt-get install casacore-dev python3-casacore

# Or install via pip
pip install python-casacore
```

### macOS

Using Homebrew:

```bash
# Install casacore
brew install casacore

# Install Python bindings
pip install python-casacore
```

### Conda

If using conda, casacore installation is simpler:

```bash
conda install -c conda-forge python-casacore
```

## Verifying Installation

After installation, verify that mstools is working:

```python
import mstools
print(mstools.__version__)
```

Or test the CLI:

```bash
mstools --help
```

## Development Installation

For development, install with additional dependencies:

```bash
pip install -e ".[dev]"
```

This includes:

- Testing tools (pytest)
- Linting tools (ruff, mypy)
- Documentation tools (mkdocs, mkdocs-material)

## Troubleshooting

### casacore ImportError

If you get an error importing casacore:

```python
ImportError: No module named 'casacore'
```

Make sure casacore libraries are installed on your system before installing python-casacore.

### Permission Errors

If you encounter permission errors, try installing in a virtual environment:

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install mstools
```

## Next Steps

Continue to the [Quick Start Guide](quickstart.md) to learn how to use mstools.

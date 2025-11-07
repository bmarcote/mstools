# mstools

**mstools** is a Python library for working with Measurement Set (MS) files, commonly used in radio astronomy data processing.

## Features

- 📊 **Easy MS File Access**: Read and inspect Measurement Set metadata
- 🔧 **Data Operations**: Polarization swapping, 1-bit scaling, frequency inversion, and more
- 🔄 **Data Export**: Export MS metadata to JSON format
- 🖥️ **CLI Tools**: Command-line interface for common operations
- 📡 **Antenna Tools**: Manage antenna mount types and configurations
- ✨ **Rich Terminal UI**: Beautiful terminal displays with interactive overview

## Quick Example

```python
from mstools import Ms

# Load a Measurement Set
ms = Ms('mydata.ms')

# Display interactive overview
ms.overview()

# Access metadata
print(f"Project: {ms.projectname}")
print(f"Antennas: {ms.antennas.names}")
print(f"Sources: {ms.sources.names}")

# Export to JSON
ms.save_json('mydata.json')

# Perform operations
ms.operations.polswap('antenna_name')
ms.operations.scale1bit(['Ef', 'Wb'])
ms.operations.flag_weights(threshold=0.1)
```

## CLI Example

```bash
# View MS file overview
mstools view mydata.ms --stats

# Run operations
mstools run polswap mydata.ms antenna1
mstools run scale1bit mydata.ms Ef Wb
mstools run flag_weights mydata.ms 0.1

# Modify antenna mounts
mstools run modify_mounts mydata.ms antenna1 ALT-AZ
mstools run ysfocus mydata.ms
```

## Installation

See the [Installation Guide](getting-started/installation.md) for detailed instructions.

```bash
pip install mstools
```

## Documentation Overview

- **[Getting Started](getting-started/installation.md)**: Installation and quick start guide
- **[User Guide](guide/overview.md)**: Comprehensive usage documentation
- **[API Reference](api/ms.md)**: Detailed API documentation
- **[CLI Reference](cli/commands.md)**: Command-line interface documentation
- **[Examples](examples.md)**: Practical examples and use cases

## Requirements

- Python 3.10+
- pyrap/casacore
- astropy
- numpy
- rich
- blessed

## License

This project is licensed under the terms specified in the LICENSE file.

## Contributing

Contributions are welcome! See the [Contributing Guide](contributing.md) for details.

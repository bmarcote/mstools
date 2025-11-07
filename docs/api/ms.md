# Ms Class

The `Ms` class is the main interface for working with Measurement Set files. It provides lazy-loading access to MS metadata and operations.

## Overview

```python
from mstools import Ms

# Create an Ms instance
ms = Ms('mydata.ms')

# Access metadata
print(ms.projectname)
print(ms.antennas.names)
print(ms.sources.names)

# Display overview
ms.overview()

# Export to JSON
ms.save_json()

# Perform operations
ms.operations.polswap('antenna1')
```

## Class Reference

::: mstools.tools.msdata.Ms
    options:
      show_source: false
      members:
        - __init__
        - get_msmetadata
        - overview
        - json
        - save_json
        - msfile
        - projectname
        - freqsetup
        - antennas
        - sources
        - time
        - operations

## Properties

### msfile
Returns the Path to the Measurement Set file.

### projectname
Returns the project/experiment name from the observation table.

### freqsetup
Returns a `FreqSetup` object containing frequency setup information.

### antennas
Returns an `Antennas` collection containing all antennas in the observation.

### sources
Returns a `Sources` collection containing all sources/fields in the observation.

### time
Returns an `ObsEpoch` object with observation time information.

### operations
Returns an `OperationsProxy` that provides access to all operations with the MS file automatically bound.

## Methods

### __init__(msfile, runstats=False)
Initialize Ms object with path to MS file.

**Parameters:**

- `msfile` (str | Path): Path to the Measurement Set file
- `runstats` (bool): If True, runs statistics tool (default False)

### get_msmetadata()
Read metadata from the MS file and populate internal attributes. Called automatically during initialization.

### overview()
Display an interactive fullscreen terminal overview of the MS file. Use arrow keys to scroll, 'Q' to quit.

**Returns:** `bool` - True if completed successfully, False if cancelled.

### json()
Convert all MS metadata to a JSON-compatible dictionary.

**Returns:** `dict` - Dictionary containing all MS information.

### save_json(filepath=None, indent=2)
Save MS metadata to a JSON file.

**Parameters:**

- `filepath` (str | Path | None): Output path (default: `<msfile>.json`)
- `indent` (int): JSON indentation spaces (default: 2)

**Returns:** `Path` - Path to the saved JSON file.

## Usage Examples

### Basic Usage

```python
from mstools import Ms

ms = Ms('observation.ms')
print(f"Observing {len(ms.sources)} sources with {len(ms.antennas)} antennas")
```

### Working with Lazy Loading

The Ms class loads metadata lazily, so you can quickly instantiate without waiting:

```python
ms = Ms('large_dataset.ms', runstats=False)  # Fast
# Metadata is loaded on first access
print(ms.projectname)  # Loads metadata here
```

### Using Operations Proxy

The operations proxy automatically binds the MS file path:

```python
# Direct operation call
from mstools.tools.operations import polswap
polswap('mydata.ms', 'antenna1')

# Via Ms.operations (cleaner)
ms = Ms('mydata.ms')
ms.operations.polswap('antenna1')  # msfile is auto-bound
```

## See Also

- [Data Classes](data-classes.md) - FreqSetup, ObsEpoch, Sources, Antennas
- [Operations](operations.md) - Available operations
- [Examples](../examples.md) - Practical usage examples

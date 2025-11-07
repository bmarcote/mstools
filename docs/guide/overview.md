# User Guide Overview

This guide provides comprehensive documentation for using mstools effectively.

## What is mstools?

mstools is a Python library designed to simplify working with Measurement Set (MS) files, the standard format for radio astronomy visibility data. It provides:

- **Easy metadata access**: Read MS information without CASA
- **Data operations**: Fix common data issues (polarization swaps, 1-bit scaling, etc.)
- **Data export**: Convert MS metadata to JSON
- **CLI tools**: Command-line interface for batch processing
- **Mount management**: Handle antenna mount types correctly

## Key Concepts

### Measurement Sets

Measurement Sets are hierarchical table structures containing:

- **Main table**: Visibility data, flags, weights
- **Subtables**: Antenna, field, spectral window, observation info, etc.

mstools reads these tables using python-casacore (pyrap) and presents them in a pythonic interface.

### The Ms Class

The central object in mstools is the `Ms` class:

```python
from mstools import Ms

ms = Ms('mydata.ms')
```

This provides access to:

- Metadata (antennas, sources, frequency setup, time range)
- Operations (polswap, scaling, flagging, etc.)
- Export functions (JSON)
- Display tools (interactive overview)

### Lazy Loading

The `Ms` class loads metadata lazily:

```python
ms = Ms('huge_dataset.ms')  # Fast - doesn't read all metadata yet
print(ms.projectname)        # Now loads metadata
```

This makes working with large datasets efficient.

## Common Tasks

### Inspecting MS Files

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Interactive overview
ms.overview()

# Programmatic access
print(f"Project: {ms.projectname}")
print(f"Antennas: {', '.join(ms.antennas.names)}")
print(f"Sources: {', '.join(ms.sources.names)}")
print(f"Frequency: {ms.freqsetup.meanfreq}")
```

### Fixing Data Issues

```python
# Polarization swap (common in VLBI)
ms.operations.polswap('antenna_with_wrong_pols')

# 1-bit scaling (for digital backends)
ms.operations.scale1bit(['Ef', 'Wb'])

# Frequency inversion
ms.operations.invert_subband('antenna_with_inverted_bands')
```

### Exporting Metadata

```python
# Get JSON dictionary
data = ms.json()

# Save to file
ms.save_json('metadata.json')
```

### Batch Processing

Use the CLI for processing multiple files:

```bash
for ms in *.ms; do
    mstools run scale1bit "$ms" Ef Wb
    mstools run polswap "$ms" problematic_ant
    mstools run flag_weights "$ms" 0.1
done
```

## Best Practices

### 1. Always Backup Data

Operations modify MS files in-place. Always keep backups:

```bash
cp -r original.ms backup.ms
```

### 2. Verify Changes

Use the overview or specific checks after operations:

```python
ms = Ms('modified.ms')
ms.overview()  # Visual check
```

### 3. Use Time Constraints

When possible, limit operations to specific time ranges:

```python
from datetime import datetime

start = datetime(2023, 1, 15, 10, 0, 0)
end = datetime(2023, 1, 15, 14, 0, 0)
ms.operations.polswap('Ef', starttime=start, endtime=end)
```

### 4. Test First

For destructive operations, test on a small dataset first:

```python
# Test flag_weights in dry-run mode
stats = ms.operations.flag_weights(0.1, apply=False)
print(f"Would flag {stats[1]:.2f}% of data")

# Then apply if acceptable
ms.operations.flag_weights(0.1, apply=True)
```

## Performance Tips

### 1. Disable Stats for Large Files

```python
# Fast initialization
ms = Ms('huge.ms', runstats=False)

# vs slow initialization
ms = Ms('huge.ms', runstats=True)  # Scans all data
```

### 2. Use Chunked Processing

For custom operations, process data in chunks:

```python
from mstools.tools.misc import table, chunkert

with table('mydata.ms', readonly=False) as ms:
    for start, nrow in chunkert(0, len(ms), 1000):
        data = ms.getcol('DATA', startrow=start, nrow=nrow)
        # Process data...
        ms.putcol('DATA', processed_data, startrow=start, nrow=nrow)
```

## Next Steps

- [Working with MS Files](ms-files.md) - Detailed MS manipulation
- [Operations Guide](operations.md) - Complete operations reference
- [Export Guide](export.md) - Exporting and analyzing metadata
- [API Reference](../api/ms.md) - Full API documentation

# Quick Start

This guide will help you get started with mstools in just a few minutes.

## Basic Usage

### Loading a Measurement Set

```python
from mstools import Ms

# Load an MS file
ms = Ms('mydata.ms')

# Access basic information
print(f"Project: {ms.projectname}")
print(f"Observation time: {ms.time.starttime} - {ms.time.endtime}")
print(f"Duration: {ms.time.duration}")
```

### Viewing MS Information

Interactive terminal overview:

```python
# Display interactive fullscreen overview
ms.overview()
# Press any key to navigate, 'Q' to quit
```

### Accessing Metadata

```python
# Frequency setup
print(f"Central frequency: {ms.freqsetup.meanfreq}")
print(f"Bandwidth: {ms.freqsetup.bandwidth}")
print(f"Subbands: {ms.freqsetup.nspw}")
print(f"Channels per subband: {ms.freqsetup.nchan}")
print(f"Polarizations: {[p.name for p in ms.freqsetup.polarizations]}")

# Antennas
print(f"All antennas: {ms.antennas.names}")
print(f"Observing antennas: {ms.antennas.observed}")

# Check if antenna observed
if 'Ef' in ms.antennas:
    print("Effelsberg observed!")

# Sources
for src in ms.sources:
    print(f"{src.name}: {src.coordinates}")
```

## Common Operations

### Polarization Swap

Fix incorrectly labeled polarizations:

```python
from datetime import datetime

# Swap polarizations for specific antenna
ms.operations.polswap('antenna_name')

# With time range
start = datetime(2023, 1, 15, 10, 30)
end = datetime(2023, 1, 15, 14, 30)
ms.operations.polswap('antenna_name', starttime=start, endtime=end)
```

### 1-bit Scaling

Correct for quantization losses in 1-bit data:

```python
# Scale single antenna
ms.operations.scale1bit('Ef')

# Scale multiple antennas
ms.operations.scale1bit(['Ef', 'Wb'])

# Undo scaling
ms.operations.scale1bit(['Ef', 'Wb'], undo=True)
```

### Frequency Inversion

Invert frequency subbands:

```python
# Invert for one antenna
ms.operations.invert_subband('antenna_name')

# Invert for multiple antennas with time range
ms.operations.invert_subband(['ant1', 'ant2'], starttime=start, endtime=end)
```

### Flag Low-Weight Data

```python
# Flag data below threshold (dry run)
ms.operations.flag_weights(threshold=0.1, apply=False)

# Apply flags
ms.operations.flag_weights(threshold=0.1, apply=True)
```

## Export Data

### JSON Export

```python
# Get JSON dictionary
data = ms.json()
print(data['frequency_setup'])

# Save to file (default: <msfile>.json)
output_path = ms.save_json()

# Custom output path
ms.save_json('custom_output.json', indent=4)
```

## Using the CLI

### View MS Information

```bash
# Basic overview
mstools view mydata.ms

# With statistics (slower but shows which antennas observed)
mstools view mydata.ms --stats
```

### Run Operations

```bash
# Polarization swap
mstools run polswap mydata.ms antenna1
mstools run polswap mydata.ms antenna1 -t1 2023/01/15/10:30:00 -t2 2023/01/15/14:30:00

# 1-bit scaling
mstools run scale1bit mydata.ms Ef Wb
mstools run scale1bit mydata.ms Ef --undo

# Invert subbands
mstools run invert_subband mydata.ms antenna1

# Flag weights
mstools run flag_weights mydata.ms 0.1
mstools run flag_weights mydata.ms 0.1 --no-apply  # Dry run

# Change names
mstools run expname mydata.ms NEW_EXPERIMENT_NAME
mstools run srcname mydata.ms OLD_SOURCE NEW_SOURCE
```

### Antenna Mount Operations

```bash
# Print mount types
mstools run print_mounts mydata.ms

# Modify mount type
mstools run modify_mounts mydata.ms antenna1 ALT-AZ

# Fix Yebes mount
mstools run ysfocus mydata.ms

# Fix Hobart mount
mstools run hofocus mydata.ms
```

## Next Steps

- **[User Guide](../guide/overview.md)**: Learn more about advanced features
- **[API Reference](../api/ms.md)**: Detailed API documentation
- **[Examples](../examples.md)**: More practical examples

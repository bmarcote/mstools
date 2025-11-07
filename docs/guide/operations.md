# Operations Guide

Comprehensive guide to all data manipulation operations.

## Overview

Operations modify MS data in-place. Always backup your data before running operations.

All operations are accessible via:

```python
from mstools import Ms

ms = Ms('mydata.ms')
ms.operations.operation_name(...)
```

## Polarization Operations

### Polarization Swap

Fixes incorrectly labeled polarizations (R↔L or X↔Y):

```python
# Swap for entire observation
ms.operations.polswap('antenna_name')

# With time constraints
from datetime import datetime
start = datetime(2023, 1, 15, 10, 30, 0)
end = datetime(2023, 1, 15, 14, 30, 0)
ms.operations.polswap('antenna_name', starttime=start, endtime=end)
```

**Use cases:**

- Antenna with swapped polarization connectors
- Incorrect labeling at correlation
- Time-dependent polarization issues

### Copy Polarization

Copy data from one polarization to another (useful for single-pol recovery):

```python
# Copy R polarization to L
ms.operations.copy_pol('antenna_name', 'R')

# Copy L to R
ms.operations.copy_pol('antenna_name', 'L')
```

**Use cases:**

- One polarization is dead or corrupted
- Need Stokes I imaging from single pol
- CASA requires dual-pol data

## Data Correction

### 1-bit Scaling

Correct for quantization losses in 1-bit sampled data:

```python
# Scale single antenna
ms.operations.scale1bit('Ef')

# Scale multiple antennas
ms.operations.scale1bit(['Ef', 'Wb', 'Tr'])

# Scale without weights
ms.operations.scale1bit(['Ef'], scale_weights=False)

# Undo scaling
ms.operations.scale1bit(['Ef'], undo=True)
```

**Scaling factors:**

- Both antennas 1-bit: π/2 / 1.1329552 ≈ 1.387
- One antenna 1-bit: √(factor_both) ≈ 1.178

### Frequency Inversion

Invert the frequency axis for antennas with reversed subbands:

```python
# Invert single antenna
ms.operations.invert_subband('antenna_name')

# Multiple antennas
ms.operations.invert_subband(['ant1', 'ant2'])

# With time range
ms.operations.invert_subband(['ant1'], starttime=start, endtime=end)
```

**Use cases:**

- Incorrect USB/LSB labeling
- Backend frequency inversion
- Correlation errors

## Flagging

### Weight-based Flagging

Flag visibilities with low weights:

```python
# Dry run to see statistics
stats = ms.operations.flag_weights(threshold=0.1, apply=False)
total, pct_total, pct_nonzero = stats
print(f"Would flag {pct_total:.2f}% of data")

# Apply flags
ms.operations.flag_weights(threshold=0.1, apply=True)
```

**Choosing thresholds:**

- 0.05-0.1: Conservative (flag very low quality)
- 0.1-0.3: Moderate
- >0.3: Aggressive (may remove good data)

## Metadata Operations

### Change Project Name

```python
ms.operations.change_project_name('NEW_EXPERIMENT_NAME')
```

### Change Source Name

```python
ms.operations.change_source_name('OLD_NAME', 'NEW_NAME')
```

## Operation Workflows

### Post-Correlation VLBI Processing

```python
from mstools import Ms

ms = Ms('correlated.ms')

# 1. Fix mount types
from mstools.tools.mounts import fix_yebes_mount, fix_hobart_mount
fix_yebes_mount('correlated.ms')
fix_hobart_mount('correlated.ms')

# 2. Apply 1-bit scaling
ms.operations.scale1bit(['Ef', 'Wb'])

# 3. Fix polarization issues
ms.operations.polswap('problematic_antenna')

# 4. Flag low-weight data
ms.operations.flag_weights(0.05)

# 5. Export metadata for documentation
ms.save_json('metadata.json')
```

### Handling Single-Pol Failures

```python
# One polarization is dead - copy the good one
ms.operations.copy_pol('antenna_with_bad_pol', 'R')  # Copy R to L

# Now continue with Stokes I imaging
```

### Time-Dependent Issues

```python
from datetime import datetime

# Issue only during specific scan
start = datetime(2023, 1, 15, 12, 30, 0)
end = datetime(2023, 1, 15, 13, 15, 0)

ms.operations.polswap('Ef', starttime=start, endtime=end)
```

## Safety Tips

1. **Always backup**: `cp -r original.ms backup.ms`
2. **Test first**: Use dry-run modes when available
3. **Verify changes**: Check with `ms.overview()` after operations
4. **Document changes**: Keep notes on what operations were applied
5. **One step at a time**: Don't chain operations without verification

## See Also

- [API Reference: Operations](../api/operations.md) - Detailed API docs
- [CLI Reference](../cli/commands.md) - Command-line usage
- [Mount Tools](../api/mounts.md) - Antenna mount operations

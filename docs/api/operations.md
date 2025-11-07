# Operations

Functions for manipulating data in Measurement Set files.

## Overview

All operations can be accessed in two ways:

1. **Direct import:** `from mstools.tools.operations import polswap`
2. **Via Ms.operations:** `ms.operations.polswap(...)` (recommended)

## Polarization Operations

### polswap

::: mstools.tools.operations.polswap

### copy_pol

::: mstools.tools.operations.copy_pol

## Data Correction Operations

### scale1bit

::: mstools.tools.operations.scale1bit

### invert_subband

::: mstools.tools.operations.invert_subband

## Flagging Operations

### flag_weights

::: mstools.tools.operations.flag_weights

## Metadata Operations

### change_project_name

::: mstools.tools.operations.change_project_name

### change_source_name

::: mstools.tools.operations.change_source_name

### get_polarizations

::: mstools.tools.operations.get_polarizations

## Examples

### Polarization Swap

```python
from mstools import Ms
import datetime as dt

ms = Ms('mydata.ms')

# Swap polarizations for entire observation
ms.operations.polswap('Ef')

# With time constraints
start = dt.datetime(2023, 1, 15, 10, 30, 0)
end = dt.datetime(2023, 1, 15, 14, 30, 0)
ms.operations.polswap('Ef', starttime=start, endtime=end)
```

### 1-bit Data Scaling

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Scale single antenna
ms.operations.scale1bit('Ef')

# Scale multiple antennas
ms.operations.scale1bit(['Ef', 'Wb'])

# Scale without modifying weights
ms.operations.scale1bit(['Ef', 'Wb'], scale_weights=False)

# Undo previous scaling
ms.operations.scale1bit(['Ef', 'Wb'], undo=True)
```

### Frequency Inversion

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Invert frequency axis for antenna
ms.operations.invert_subband('Tr')

# Multiple antennas with time range
ms.operations.invert_subband(['Tr', 'Sv'], starttime=start, endtime=end)
```

### Weight-based Flagging

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Dry run - see what would be flagged
stats = ms.operations.flag_weights(threshold=0.1, apply=False)
print(f"Would flag {stats[1]:.2f}% of data")

# Apply flags
ms.operations.flag_weights(threshold=0.1, apply=True)
```

### Changing Metadata

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Change project name
ms.operations.change_project_name('NEW_PROJECT_NAME')

# Change source name
ms.operations.change_source_name('old_name', 'new_name')
```

## See Also

- [Ms Class](ms.md) - Main interface
- [Mount Tools](mounts.md) - Antenna mount operations
- [CLI Reference](../cli/commands.md) - Command-line interface

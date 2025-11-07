# Mount Tools

Functions for managing antenna mount types in Measurement Sets.

## Functions

### print_mounts

::: mstools.tools.mounts.print_mounts

### modify_mounts

::: mstools.tools.mounts.modify_mounts

### fix_yebes_mount

::: mstools.tools.mounts.fix_yebes_mount

### fix_hobart_mount

::: mstools.tools.mounts.fix_hobart_mount

## Examples

### Print Mount Information

```python
from mstools.tools.mounts import print_mounts

# Display all antenna mounts
mounts = print_mounts('mydata.ms')
print(mounts)
# Output: {'Ef': 'ALT-AZ', 'Wb': 'ALT-AZ', ...}
```

### Modify Mount Type

```python
from mstools.tools.mounts import modify_mounts

# Change mount type for specific antenna
modify_mounts('mydata.ms', 'Ef', 'EQUATORIAL')
```

### Fix Yebes Mount

The Yebes 40m telescope uses a Nasmyth focus mount that requires special handling:

```python
from mstools.tools.mounts import fix_yebes_mount

# Automatically fixes Yebes mount to ALT-AZ-NASMYTH-RH
# Looks for station names: 'Ys', 'YS', or 'YEBES40M'
fix_yebes_mount('mydata.ms')
```

This sets the mount to `'ALT-AZ-NASMYTH-RH'` which allows tConvert to correctly set `MNTSTA=4` in the FITS AN table for proper parallactic angle correction.

### Fix Hobart Mount

```python
from mstools.tools.mounts import fix_hobart_mount

# Changes Hobart mount from X_YEW to X-YEW (expected by tConvert)
# Looks for station names: 'HOBART', 'HO', or 'HOB_DBBC'
fix_hobart_mount('mydata.ms')
```

## Mount Types

Common mount types include:

- **ALT-AZ**: Altitude-Azimuth mount
- **EQUATORIAL**: Equatorial mount  
- **X-YEW**: X-Y East-West mount
- **ALT-AZ-NASMYTH-RH**: Nasmyth focus (right-hand, used by Yebes)
- **ALT-AZ-NASMYTH-LH**: Nasmyth focus (left-hand)

## See Also

- [Operations](operations.md) - Other MS operations
- [CLI Reference](../cli/commands.md) - Command-line mount tools

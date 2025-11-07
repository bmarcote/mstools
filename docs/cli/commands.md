# CLI Reference

The mstools command-line interface provides convenient access to common operations.

## Overview

```bash
mstools [command] [options]
```

## Commands

### view

Display an overview of an MS file.

```bash
mstools view <msfile> [--stats]
```

**Arguments:**

- `msfile`: Path to the Measurement Set file

**Options:**

- `-s, --stats`: Check which antennas actually observed (slower but more accurate)

**Examples:**

```bash
# Basic overview
mstools view mydata.ms

# With statistics
mstools view mydata.ms --stats
```

### run

Execute an operation on an MS file.

```bash
mstools run <tool> <msfile> [arguments] [options]
```

## Available Tools

### polswap

Swap polarizations for a specified antenna.

```bash
mstools run polswap <msfile> <antenna> [-t1 START] [-t2 END]
```

**Arguments:**

- `msfile`: MS file to process
- `antenna`: Antenna name to swap polarizations

**Options:**

- `-t1, --starttime`: Start time (YYYY/MM/DD/hh:mm:ss or YYYY/DOY/hh:mm:ss)
- `-t2, --endtime`: End time (YYYY/MM/DD/hh:mm:ss or YYYY/DOY/hh:mm:ss)

**Examples:**

```bash
# Swap for entire observation
mstools run polswap mydata.ms Ef

# With time constraints
mstools run polswap mydata.ms Ef -t1 2023/01/15/10:30:00 -t2 2023/01/15/14:30:00
mstools run polswap mydata.ms Ef -t1 2023/015/10:30 -t2 2023/015/14:30
```

### copypol

Copy data from one polarization to another.

```bash
mstools run copypol <msfile> <antenna> <polfrom>
```

**Arguments:**

- `msfile`: MS file to process
- `antenna`: Antenna name
- `polfrom`: Polarization to copy from (R, L, X, or Y)

**Examples:**

```bash
mstools run copypol mydata.ms Ef R
mstools run copypol mydata.ms Wb L
```

### scale1bit

Scale 1-bit data for quantization correction.

```bash
mstools run scale1bit <msfile> <antenna> [antenna...] [--undo] [--no-scale-weights]
```

**Arguments:**

- `msfile`: MS file to process
- `antenna`: One or more antenna names

**Options:**

- `--undo`: Undo the scaling
- `--no-scale-weights`: Do not scale weights

**Examples:**

```bash
# Scale single antenna
mstools run scale1bit mydata.ms Ef

# Scale multiple antennas
mstools run scale1bit mydata.ms Ef Wb Tr

# Undo scaling
mstools run scale1bit mydata.ms Ef --undo

# Scale without weights
mstools run scale1bit mydata.ms Ef --no-scale-weights
```

### invert_subband

Invert frequency subbands for specified antennas.

```bash
mstools run invert_subband <msfile> <antenna> [antenna...] [-t1 START] [-t2 END]
```

**Arguments:**

- `msfile`: MS file to process
- `antenna`: One or more antenna names

**Options:**

- `-t1, --starttime`: Start time
- `-t2, --endtime`: End time

**Examples:**

```bash
mstools run invert_subband mydata.ms Tr
mstools run invert_subband mydata.ms Tr Sv -t1 2023/01/15/10:00
```

### flag_weights

Flag data based on weight threshold.

```bash
mstools run flag_weights <msfile> <threshold> [--no-apply]
```

**Arguments:**

- `msfile`: MS file to process
- `threshold`: Weight threshold (0-1)

**Options:**

- `--no-apply`: Dry run, do not apply flags

**Examples:**

```bash
# Dry run
mstools run flag_weights mydata.ms 0.1 --no-apply

# Apply flags
mstools run flag_weights mydata.ms 0.1
```

### expname

Change the project/experiment name.

```bash
mstools run expname <msfile> <new_name>
```

**Examples:**

```bash
mstools run expname mydata.ms EG123A
```

### srcname

Change the name of a specific source.

```bash
mstools run srcname <msfile> <src_name> <new_name>
```

**Examples:**

```bash
mstools run srcname mydata.ms 3C84 PERSEUS_A
```

### print_mounts

Print antenna mount information.

```bash
mstools run print_mounts <msfile>
```

**Examples:**

```bash
mstools run print_mounts mydata.ms
```

### modify_mounts

Modify antenna mount type.

```bash
mstools run modify_mounts <msfile> <antenna> <mount>
```

**Arguments:**

- `msfile`: MS file to modify
- `antenna`: Antenna name
- `mount`: Mount type (e.g., ALT-AZ, EQUATORIAL, X-YEW)

**Examples:**

```bash
mstools run modify_mounts mydata.ms Ef EQUATORIAL
```

### ysfocus

Fix Yebes antenna mount type.

```bash
mstools run ysfocus <msfile>
```

**Examples:**

```bash
mstools run ysfocus mydata.ms
```

### hofocus

Fix Hobart antenna mount type.

```bash
mstools run hofocus <msfile>
```

**Examples:**

```bash
mstools run hofocus mydata.ms
```

## Common Workflows

### Typical Post-Correlation Processing

```bash
# 1. View the MS
mstools view correlated.ms --stats

# 2. Fix any mount issues
mstools run ysfocus correlated.ms
mstools run hofocus correlated.ms

# 3. Apply 1-bit scaling if needed
mstools run scale1bit correlated.ms Ef Wb

# 4. Fix polarization issues
mstools run polswap correlated.ms problematic_antenna

# 5. Flag low-weight data
mstools run flag_weights correlated.ms 0.05
```

## See Also

- [Python API](../api/ms.md) - Using mstools in Python scripts
- [Operations](../api/operations.md) - Detailed operation documentation

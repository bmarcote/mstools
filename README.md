# MS Tools

Tools to work with Measurement Set (MS) files and data diagnostics.

## Installation

```bash
pip install -e .
```

## Usage

### Command Line Interface

The package provides a `mstools` command with two main subcommands:

#### Overview Command

Display an overview of MS file contents:

```bash
mstools overview <ms_file> [-i]
```

Options:
- `-i, --ignore`: Ignore checking which antennas observed (faster)

#### Run Command

Execute specific tools on MS files:

```bash
mstools run <tool> [tool-specific arguments]
```

Available tools:

**polswap** - Swap polarizations for specified antennas:
```bash
mstools run polswap <ms_file> <antenna> [-t1 START_TIME] [-t2 END_TIME]
```

**mssplit** - Extract sources from MS file:
```bash
mstools run mssplit <ms_file> <source_names>
```

**add_intent** - Add intent/state IDs to MS:
```bash
mstools run add_intent <ms_file> <source_ids> <state_ids>
```

**print_mounts** - Print antenna mount information:
```bash
mstools run print_mounts <ms_file>
```

### Python API

All tools can be imported and used in Python:

```python
from mstools import msoverview, polswap, mssplit, add_intent, print_mounts

# Display MS overview
msoverview('observation.ms', ignore=False)

# Swap polarizations
polswap('observation.ms', 'Ef', starttime='2023/01/15/10:00:00', endtime='2023/01/15/12:00:00')

# Split MS by source
mssplit('observation.ms', 'J1234+5678')

# Add intent/state IDs
add_intent('observation.ms', '0,1,2', '10,11,12')

# Print mount info
print_mounts('observation.ms')
```

## Tools Description

- **msoverview**: Provides a comprehensive overview of MS file contents including antennas, sources, frequency setup, and observation times.
- **polswap**: Swaps polarizations for specified antennas (fixes R/L or X/Y labeling issues).
- **mssplit**: Extracts specific sources from an MS file using CASA mstransform.
- **add_intent**: Modifies state IDs for specified sources in the MS.
- **print_mounts**: Displays mount type information for all antennas.

## Author

Benito Marcote (marcote@jive.eu)

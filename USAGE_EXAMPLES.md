# mstools Usage Examples

## Quick Reference

### CLI Usage

```bash
# Display MS overview
mstools overview observation.ms
mstools overview observation.ms -s  # Slower, but it will get some stats of the data (TO BE IMPLEMENTED)

# Swap polarizations
mstools run polswap observation.ms Ef
mstools run polswap observation.ms Ef -t1 2023/01/15/10:00:00 -t2 2023/01/15/12:00:00

# Split MS by source
mstools run mssplit observation.ms "J1234+5678,J0000+0000"

# Add intent/state IDs
mstools run add_intent observation.ms "0,1,2" "10,11,12"

# Print antenna mounts
mstools run print_mounts observation.ms
```

### Python API Usage

```python
import mstools

# Overview
mstools.msoverview('observation.ms')
mstools.msoverview('observation.ms', ignore=True)

# Polswap
mstools.polswap('observation.ms', 'Ef')
mstools.polswap('observation.ms', 'Ef', starttime='2023/01/15/10:00:00', endtime='2023/01/15/12:00:00')

# Mssplit
mstools.mssplit('observation.ms', 'J1234+5678')

# Add intent
mstools.add_intent('observation.ms', '0,1,2', '10,11,12')

# Print mounts
mstools.print_mounts('observation.ms')
```

### Direct Import

```python
from mstools.tools import polswap, mssplit, msoverview, add_intent, print_mounts

# Use any tool directly
polswap('observation.ms', 'Ef')
```

## Installation

```bash
# Install in development mode
cd /Users/hawky/Programing/mstools
pip install -e .

# The 'mstools' command will be available after installation
```

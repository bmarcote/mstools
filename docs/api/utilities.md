# Utilities

Utility functions for working with Measurement Set data.

## Time Utilities

### mjd2date

::: mstools.tools.misc.mjd2date

### date2mjd

::: mstools.tools.misc.date2mjd

### parse_time

::: mstools.tools.misc.parse_time

## Data Processing Utilities

### chunkert

::: mstools.tools.misc.chunkert

## Table Context Manager

### table

::: mstools.tools.misc.table

## Examples

### Working with Time Conversions

```python
from mstools.tools.misc import mjd2date, date2mjd, parse_time
import datetime as dt

# Convert MJD to datetime
mjd = 59945.5
date = mjd2date(mjd)
print(f"MJD {mjd} = {date}")

# Convert datetime to MJD
date = dt.datetime(2023, 1, 15, 12, 0, 0)
mjd = date2mjd(date)
print(f"{date} = MJD {mjd}")

# Parse time strings
time1 = parse_time("2023/01/15/12:30:45")  # YYYY/MM/DD/hh:mm:ss
time2 = parse_time("2023/015/12:30")       # YYYY/DOY/hh:mm
print(time1, time2)
```

### Using the Table Context Manager

```python
from mstools.tools.misc import table
from pathlib import Path

msfile = Path('mydata.ms')

# Safe table access with automatic cleanup
with table(msfile, readonly=True) as ms:
    # Read data
    ant_table_path = ms.getkeyword('ANTENNA')
    
    with table(ant_table_path) as ant_table:
        antenna_names = ant_table.getcol('NAME')
        print(f"Antennas: {antenna_names}")
    
# Tables are automatically closed, even if exceptions occur
```

### Chunked Data Processing

```python
from mstools.tools.misc import chunkert, table

with table('mydata.ms', readonly=False) as ms:
    total_rows = len(ms)
    chunk_size = 1000
    
    # Process data in chunks to manage memory
    for start, nrow in chunkert(0, total_rows, chunk_size):
        # Read chunk
        data = ms.getcol('DATA', startrow=start, nrow=nrow)
        
        # Process data
        processed_data = data * 2.0
        
        # Write back
        ms.putcol('DATA', processed_data, startrow=start, nrow=nrow)
        
        print(f"Processed rows {start} to {start + nrow}")
```

## See Also

- [Operations](operations.md) - Data manipulation operations
- [Ms Class](ms.md) - Main interface

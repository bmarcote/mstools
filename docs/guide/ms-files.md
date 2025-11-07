# Working with MS Files

Learn how to access and manipulate Measurement Set metadata.

## Accessing Metadata

### Basic Information

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Project and observation info
print(f"Project: {ms.projectname}")
print(f"Start: {ms.time.starttime}")
print(f"End: {ms.time.endtime}")
print(f"Duration: {ms.time.duration}")
print(f"MJD: {ms.time.mjd}")
```

### Frequency Setup

```python
# Access frequency information
freq = ms.freqsetup

print(f"Central frequency: {freq.meanfreq}")
print(f"Bandwidth: {freq.bandwidth}")
print(f"Subbands: {freq.nspw}")
print(f"Channels per subband: {freq.nchan}")
print(f"Polarizations: {[p.name for p in freq.polarizations]}")

# Unit conversions
from astropy import units as u
freq_ghz = freq.meanfreq.to(u.GHz)
bw_mhz = freq.bandwidth.to(u.MHz)
```

### Antennas

```python
# List all antennas
print(ms.antennas.names)

# Get specific antenna
ef = ms.antennas['Ef']
print(f"{ef.name}: observed={ef.observed}")

# Check if antenna observed
if 'Ef' in ms.antennas:
    print("Effelsberg is in the array")

# Iterate over antennas
for ant in ms.antennas:
    if ant.observed:
        print(f"{ant.name} observed in subbands: {ant.subbands}")
```

### Sources

```python
# List all sources
print(ms.sources.names)

# Access by index or name
first_source = ms.sources[0]
target = ms.sources['3C84']

# Get coordinates
for src in ms.sources:
    ra = src.coordinates.ra.deg
    dec = src.coordinates.dec.deg
    print(f"{src.name}: RA={ra:.4f}°, Dec={dec:.4f}°")
    
    # Convert to HMS/DMS
    ra_hms = src.coordinates.ra.to_string(unit=u.hourangle, sep=':')
    dec_dms = src.coordinates.dec.to_string(unit=u.deg, sep=':')
    print(f"  {ra_hms}, {dec_dms}")
```

## Interactive Overview

The `overview()` method provides a fullscreen interactive display:

```python
ms.overview()
```

Features:

- Scrollable with arrow keys
- Press 'Q' to quit
- Shows all metadata in organized sections
- Color-coded display

## Direct Table Access

For advanced use, access tables directly:

```python
from mstools.tools.misc import table

# Read main table
with table('mydata.ms') as ms:
    # Get antenna subtable
    with table(ms.getkeyword('ANTENNA')) as ant:
        names = ant.getcol('NAME')
        positions = ant.getcol('POSITION')
        
# Read-write access
with table('mydata.ms', readonly=False) as ms:
    # Modify data
    data = ms.getcol('DATA', startrow=0, nrow=100)
    # ... process data ...
    ms.putcol('DATA', modified_data, startrow=0, nrow=100)
```

## Working with Multiple MS Files

```python
import glob
from mstools import Ms

# Process multiple files
ms_files = glob.glob('*.ms')

for ms_path in ms_files:
    ms = Ms(ms_path)
    print(f"\n{ms_path}:")
    print(f"  Antennas: {', '.join(ms.antennas.observed)}")
    print(f"  Sources: {', '.join(ms.sources.names)}")
    print(f"  Duration: {ms.time.duration}")
```

## See Also

- [Operations Guide](operations.md) - Data manipulation
- [Export Guide](export.md) - Exporting metadata
- [API Reference](../api/ms.md) - Complete API documentation

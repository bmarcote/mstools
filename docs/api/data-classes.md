# Data Classes

mstools provides several dataclasses for representing Measurement Set metadata.

## ObsEpoch

Represents an observation epoch with start and end times.

::: mstools.tools.msdata.ObsEpoch

**Example:**

```python
from mstools import Ms

ms = Ms('mydata.ms')
epoch = ms.time

print(f"Start: {epoch.starttime}")
print(f"End: {epoch.endtime}")
print(f"Date: {epoch.epoch}")
print(f"MJD: {epoch.mjd}")
print(f"Day of Year: {epoch.doy}")
print(f"Duration: {epoch.duration}")
```

## Source

Represents an astronomical source/field in an observation.

::: mstools.tools.msdata.Source

**Example:**

```python
from mstools import Ms

ms = Ms('mydata.ms')
for src in ms.sources:
    print(f"{src.name}: {src.coordinates}")
    print(f"  RA: {src.coordinates.ra.deg}°")
    print(f"  Dec: {src.coordinates.dec.deg}°")
    if src.intent:
        print(f"  Intent: {src.intent}")
```

## Sources

Container class for Source objects with convenient access by name or index.

::: mstools.tools.msdata.Sources

**Example:**

```python
from mstools import Ms

ms = Ms('mydata.ms')
sources = ms.sources

# Access by index
first_source = sources[0]

# Access by name
target = sources['3C84']

# Check if source exists
if '3C84' in sources:
    print("3C84 is in the observation")

# Get all source names
print(sources.names)

# Get all coordinates
print(sources.coordinates)
```

## Antenna

Dataclass representing an antenna.

::: mstools.tools.msdata.Antenna

**Example:**

```python
from mstools import Ms

ms = Ms('mydata.ms')
for ant in ms.antennas:
    print(f"{ant.name}: observed={ant.observed}, subbands={ant.subbands}")
```

## Antennas

Container class for Antenna objects with convenient access.

::: mstools.tools.msdata.Antennas

**Example:**

```python
from mstools import Ms

ms = Ms('mydata.ms')
antennas = ms.antennas

# Access by index
first_antenna = antennas[0]

# Access by name
ef = antennas['Ef']

# Check if antenna observed
if 'Ef' in antennas:
    print("Effelsberg is in the array")

# Get all antenna names
print(antennas.names)

# Get only observing antennas
print(antennas.observed)

# Iterate over antennas
for ant in antennas:
    if ant.observed:
        print(f"{ant.name} observed")
```

## FreqSetup

Defines the frequency setup of an observation.

::: mstools.tools.msdata.FreqSetup

**Example:**

```python
from mstools import Ms

ms = Ms('mydata.ms')
freq = ms.freqsetup

print(f"Central frequency: {freq.meanfreq}")
print(f"Bandwidth: {freq.bandwidth}")
print(f"Number of subbands: {freq.nspw}")
print(f"Channels per subband: {freq.nchan}")
print(f"Polarizations: {[p.name for p in freq.polarizations]}")

# Convert to specific units
from astropy import units as u
freq_ghz = freq.meanfreq.to(u.GHz)
print(f"Frequency: {freq_ghz:.2f}")
```

## Stokes

Enum class for Stokes/polarization types.

::: mstools.tools.misc.Stokes

**Example:**

```python
from mstools.tools.misc import Stokes

# Access Stokes parameters
rr = Stokes.RR
ll = Stokes.LL
xx = Stokes.XX
yy = Stokes.YY

# Check polarization type
from mstools import Ms
ms = Ms('mydata.ms')

for pol in ms.freqsetup.polarizations:
    if pol in (Stokes.RR, Stokes.LL):
        print(f"{pol.name}: Circular polarization")
    elif pol in (Stokes.XX, Stokes.YY):
        print(f"{pol.name}: Linear polarization")
```

## See Also

- [Ms Class](ms.md) - Main interface for working with MS files
- [Operations](operations.md) - Available operations

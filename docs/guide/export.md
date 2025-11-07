# Export Data

Learn how to export MS metadata for analysis, documentation, and archiving.

## JSON Export

mstools can export all MS metadata to JSON format:

```python
from mstools import Ms

ms = Ms('mydata.ms')

# Get JSON dictionary
data = ms.json()

# Save to file (default: <msfile>.json)
output_path = ms.save_json()

# Custom output path
ms.save_json('custom_name.json', indent=4)
```

## JSON Structure

The exported JSON contains:

```json
{
  "msfile": "/path/to/mydata.ms",
  "projectname": "EG123A",
  "observation": {
    "starttime": "2023-01-15T10:30:00",
    "endtime": "2023-01-15T14:30:00",
    "epoch": "2023-01-15",
    "mjd": 59945.4375,
    "doy": 15,
    "duration_hours": 4.0
  },
  "frequency_setup": {
    "mean_frequency": 22236000000.0,
    "bandwidth": 128000000.0,
    "n_subbands": 8,
    "n_channels": 256,
    "polarizations": ["RR", "LL"]
  },
  "sources": [
    {
      "name": "3C84",
      "ra_deg": 49.95065,
      "dec_deg": 41.51167,
      "intent": "TARGET"
    }
  ],
  "antennas": [
    {
      "name": "Ef",
      "observed": true,
      "subbands": [0, 1, 2, 3, 4, 5, 6, 7]
    }
  ]
}
```

## Using Exported Data

### Python Analysis

```python
import json

# Load exported data
with open('mydata.json') as f:
    data = json.load(f)

# Analyze
freq_ghz = data['frequency_setup']['mean_frequency'] / 1e9
print(f"Observing frequency: {freq_ghz:.2f} GHz")

# Count observing antennas
n_obs = sum(1 for ant in data['antennas'] if ant['observed'])
print(f"{n_obs} antennas observed")

# Get source coordinates
for src in data['sources']:
    print(f"{src['name']}: RA={src['ra_deg']:.4f}°, Dec={src['dec_deg']:.4f}°")
```

### Pandas DataFrame

```python
import json
import pandas as pd

with open('mydata.json') as f:
    data = json.load(f)

# Create DataFrames
antennas_df = pd.DataFrame(data['antennas'])
sources_df = pd.DataFrame(data['sources'])

print(antennas_df[antennas_df['observed']])
```

### Batch Analysis

```python
import glob
import json
from collections import defaultdict

# Analyze multiple observations
stats = defaultdict(list)

for json_file in glob.glob('*.json'):
    with open(json_file) as f:
        data = json.load(f)
    
    stats['project'].append(data['projectname'])
    stats['duration'].append(data['observation']['duration_hours'])
    stats['n_antennas'].append(len([a for a in data['antennas'] if a['observed']]))
    stats['frequency'].append(data['frequency_setup']['mean_frequency'] / 1e9)

# Create summary
import pandas as pd
df = pd.DataFrame(stats)
print(df.describe())
```

## Documentation Generation

### Observation Summary

```python
import json
from mstools import Ms

ms = Ms('mydata.ms')
data = ms.json()

# Generate markdown summary
md = f"""# Observation Summary: {data['projectname']}

## Observation Details
- **Date**: {data['observation']['epoch']}
- **Start**: {data['observation']['starttime']}
- **Duration**: {data['observation']['duration_hours']:.2f} hours
- **MJD**: {data['observation']['mjd']}

## Frequency Setup
- **Central Frequency**: {data['frequency_setup']['mean_frequency']/1e9:.3f} GHz
- **Bandwidth**: {data['frequency_setup']['bandwidth']/1e6:.1f} MHz
- **Subbands**: {data['frequency_setup']['n_subbands']}
- **Channels**: {data['frequency_setup']['n_channels']}
- **Polarizations**: {', '.join(data['frequency_setup']['polarizations'])}

## Sources
"""

for src in data['sources']:
    md += f"- **{src['name']}**: RA={src['ra_deg']:.4f}°, Dec={src['dec_deg']:.4f}°\n"

md += "\n## Antennas\n"
for ant in data['antennas']:
    if ant['observed']:
        md += f"- {ant['name']}\n"

with open('observation_summary.md', 'w') as f:
    f.write(md)
```

## Archive Integration

### Create Observation Archive

```python
import json
import shutil
from pathlib import Path
from mstools import Ms

def archive_observation(msfile, archive_dir):
    """Archive an observation with metadata."""
    ms = Ms(msfile)
    
    # Create archive directory
    obs_name = ms.projectname
    obs_dir = Path(archive_dir) / obs_name
    obs_dir.mkdir(parents=True, exist_ok=True)
    
    # Export metadata
    json_path = obs_dir / f"{obs_name}_metadata.json"
    ms.save_json(json_path)
    
    # Copy MS file (optional - can be large!)
    # shutil.copytree(msfile, obs_dir / Path(msfile).name)
    
    print(f"Archived {obs_name} to {obs_dir}")
    return json_path

# Usage
archive_observation('mydata.ms', '/archive/observations')
```

### Build Observation Database

```python
import json
import glob
import sqlite3

# Create database
conn = sqlite3.connect('observations.db')
c = conn.cursor()

c.execute('''CREATE TABLE IF NOT EXISTS observations
             (project text, epoch text, duration real, 
              frequency real, n_antennas integer, n_sources integer)''')

# Import JSON files
for json_file in glob.glob('/archive/*/*.json'):
    with open(json_file) as f:
        data = json.load(f)
    
    c.execute('''INSERT INTO observations VALUES (?,?,?,?,?,?)''',
              (data['projectname'],
               data['observation']['epoch'],
               data['observation']['duration_hours'],
               data['frequency_setup']['mean_frequency'],
               len([a for a in data['antennas'] if a['observed']]),
               len(data['sources'])))

conn.commit()
conn.close()
```

## Custom Export Formats

### CSV Export

```python
import csv
from mstools import Ms

ms = Ms('mydata.ms')
data = ms.json()

# Export antennas to CSV
with open('antennas.csv', 'w', newline='') as f:
    writer = csv.DictWriter(f, fieldnames=['name', 'observed', 'subbands'])
    writer.writeheader()
    for ant in data['antennas']:
        writer.writerow({
            'name': ant['name'],
            'observed': ant['observed'],
            'subbands': ','.join(map(str, ant['subbands']))
        })
```

## See Also

- [Ms Class](../api/ms.md#json) - JSON export API
- [Working with MS Files](ms-files.md) - Accessing metadata
- [Examples](../examples.md) - More practical examples

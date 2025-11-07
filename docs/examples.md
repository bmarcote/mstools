# Examples

Practical examples for common mstools use cases.

## Basic Examples

### Quick MS Inspection

```python
from mstools import Ms

# Load and display overview
ms = Ms('observation.ms')
ms.overview()  # Interactive display

# Print basic info
print(f"Project: {ms.projectname}")
print(f"Antennas: {', '.join(ms.antennas.observed)}")
print(f"Sources: {', '.join(ms.sources.names)}")
print(f"Frequency: {ms.freqsetup.meanfreq.to('GHz')}")
```

### Compare Multiple Observations

```python
import glob
from mstools import Ms

for ms_file in glob.glob('*.ms'):
    ms = Ms(ms_file)
    print(f"\n{ms_file}:")
    print(f"  Date: {ms.time.epoch}")
    print(f"  Duration: {ms.time.duration}")
    print(f"  Antennas: {len(ms.antennas.observed)}")
    print(f"  Frequency: {ms.freqsetup.meanfreq.to('GHz'):.2f}")
```

## VLBI Processing

### Post-Correlation Pipeline

```python
from mstools import Ms
from mstools.tools.mounts import fix_yebes_mount, fix_hobart_mount

def process_correlated_ms(msfile):
    """Complete post-correlation processing pipeline."""
    
    print(f"Processing {msfile}...")
    ms = Ms(msfile)
    
    # 1. Fix mount types
    print("  Fixing mount types...")
    try:
        fix_yebes_mount(msfile, verbose=False)
    except ValueError:
        print("    No Yebes antenna found")
    
    try:
        fix_hobart_mount(msfile, verbose=False)
    except ValueError:
        print("    No Hobart antenna found")
    
    # 2. Apply 1-bit scaling to specific antennas
    onebit_antennas = ['Ef', 'Wb', 'Tr']
    present_onebit = [a for a in onebit_antennas if a in ms.antennas]
    
    if present_onebit:
        print(f"  Scaling 1-bit antennas: {', '.join(present_onebit)}")
        ms.operations.scale1bit(present_onebit)
    
    # 3. Apply weight-based flagging
    print("  Flagging low-weight data...")
    stats = ms.operations.flag_weights(0.05, apply=True)
    print(f"    Flagged {stats[1]:.2f}% of visibilities")
    
    # 4. Export metadata
    print("  Exporting metadata...")
    json_file = ms.save_json()
    print(f"    Saved to {json_file}")
    
    print("  Done!\n")

# Process all MS files
import glob
for msfile in glob.glob('*.ms'):
    process_correlated_ms(msfile)
```

### Fix Problematic Antenna

```python
from mstools import Ms
from datetime import datetime

ms = Ms('observation.ms')

# Antenna Ef has wrong polarizations during scan 2
# Time range for scan 2
start = datetime(2023, 1, 15, 11, 30, 0)
end = datetime(2023, 1, 15, 12, 15, 0)

# Fix the polarization
ms.operations.polswap('Ef', starttime=start, endtime=end)

# Verify
ms.overview()
```

## Data Analysis

### Generate Observation Report

```python
from mstools import Ms
import json

def generate_report(msfile, output_md):
    """Generate a markdown observation report."""
    
    ms = Ms(msfile)
    data = ms.json()
    
    # Calculate statistics
    obs_antennas = [a['name'] for a in data['antennas'] if a['observed']]
    freq_ghz = data['frequency_setup']['mean_frequency'] / 1e9
    bw_mhz = data['frequency_setup']['bandwidth'] / 1e6
    
    # Generate report
    report = f"""# Observation Report: {data['projectname']}

## Summary
- **Date**: {data['observation']['epoch']}
- **Duration**: {data['observation']['duration_hours']:.2f} hours  
- **Frequency**: {freq_ghz:.3f} GHz
- **Bandwidth**: {bw_mhz:.1f} MHz × {data['frequency_setup']['n_subbands']} subbands
- **Polarizations**: {', '.join(data['frequency_setup']['polarizations'])}

## Antennas ({len(obs_antennas)})
{', '.join(obs_antennas)}

## Sources ({len(data['sources'])})
"""
    
    for src in data['sources']:
        report += f"- **{src['name']}**: "
        report += f"RA {src['ra_deg']:.4f}°, Dec {src['dec_deg']:.4f}°\n"
    
    report += f"\n## Timeline\n"
    report += f"- Start: {data['observation']['starttime']}\n"
    report += f"- End: {data['observation']['endtime']}\n"
    report += f"- MJD: {data['observation']['mjd']:.4f}\n"
    
    # Save report
    with open(output_md, 'w') as f:
        f.write(report)
    
    print(f"Report saved to {output_md}")

# Usage
generate_report('observation.ms', 'observation_report.md')
```

### Array Configuration Analysis

```python
from mstools import Ms
import numpy as np

def analyze_array(msfile):
    """Analyze array configuration."""
    
    ms = Ms(msfile)
    
    print(f"Array Analysis for {ms.projectname}\n")
    print(f"Number of antennas: {len(ms.antennas)}")
    print(f"Observing antennas: {len(ms.antennas.observed)}")
    
    # Polarization summary
    pols = [p.name for p in ms.freqsetup.polarizations]
    print(f"\nPolarization products: {', '.join(pols)}")
    
    # Check for circular vs linear
    if any(p in ['RR', 'LL', 'RL', 'LR'] for p in pols):
        print("  Type: Circular")
    elif any(p in ['XX', 'YY', 'XY', 'YX'] for p in pols):
        print("  Type: Linear")
    
    # Subband coverage per antenna
    print("\nSubband coverage:")
    for ant in ms.antennas:
        if ant.observed:
            coverage = len(ant.subbands) / ms.freqsetup.nspw * 100
            print(f"  {ant.name}: {len(ant.subbands)}/{ms.freqsetup.nspw} ({coverage:.0f}%)")
    
    # Number of baselines
    n_ant = len(ms.antennas.observed)
    n_baselines = n_ant * (n_ant - 1) // 2
    print(f"\nNumber of baselines: {n_baselines}")
    
    return {
        'n_antennas': n_ant,
        'n_baselines': n_baselines,
        'polarizations': pols
    }

# Usage
stats = analyze_array('observation.ms')
```

## Batch Processing

### Process Multiple Observations

```python
#!/usr/bin/env python3
"""Process all MS files in a directory."""

import glob
import sys
from pathlib import Path
from mstools import Ms

def process_directory(input_dir, output_dir):
    """Process all MS files in a directory."""
    
    input_path = Path(input_dir)
    output_path = Path(output_dir)
    output_path.mkdir(parents=True, exist_ok=True)
    
    ms_files = list(input_path.glob('*.ms'))
    print(f"Found {len(ms_files)} MS files\n")
    
    for i, msfile in enumerate(ms_files, 1):
        print(f"[{i}/{len(ms_files)}] Processing {msfile.name}...")
        
        try:
            ms = Ms(str(msfile))
            
            # Export JSON
            json_name = msfile.stem + '.json'
            json_path = output_path / json_name
            ms.save_json(json_path)
            
            # Apply operations
            ms.operations.flag_weights(0.05, apply=True)
            
            print(f"  ✓ Completed\n")
            
        except Exception as e:
            print(f"  ✗ Error: {e}\n")
            continue
    
    print("All files processed!")

if __name__ == '__main__':
    if len(sys.argv) != 3:
        print("Usage: python process_batch.py <input_dir> <output_dir>")
        sys.exit(1)
    
    process_directory(sys.argv[1], sys.argv[2])
```

### Export All to JSON

```bash
#!/bin/bash
# Export all MS files to JSON

for ms in *.ms; do
    echo "Exporting $ms..."
    python3 -c "from mstools import Ms; Ms('$ms').save_json()"
done

echo "All files exported!"
```

## Advanced Examples

### Custom Operation with Chunked Processing

```python
from mstools import Ms
from mstools.tools.misc import table, chunkert
import numpy as np

def custom_scaling_operation(msfile, scale_factor):
    """Apply custom scaling to visibility data."""
    
    print(f"Scaling {msfile} by factor {scale_factor}")
    
    with table(msfile, readonly=False) as ms:
        total_rows = len(ms)
        chunk_size = 1000
        
        processed = 0
        for start, nrow in chunkert(0, total_rows, chunk_size):
            # Read data
            data = ms.getcol('DATA', startrow=start, nrow=nrow)
            
            # Apply scaling
            scaled_data = data * scale_factor
            
            # Write back
            ms.putcol('DATA', scaled_data, startrow=start, nrow=nrow)
            
            processed += nrow
            progress = processed / total_rows * 100
            print(f"  Progress: {progress:.1f}%", end='\r')
        
        print("\n  Done!")

# Usage
custom_scaling_operation('observation.ms', 1.5)
```

## See Also

- [User Guide](guide/overview.md) - Comprehensive documentation
- [API Reference](api/ms.md) - Detailed API documentation
- [CLI Reference](cli/commands.md) - Command-line usage

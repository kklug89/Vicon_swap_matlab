# swap_markers.m
## Automated Head Marker Swap Detection & Correction for Vicon Nexus
A MATLAB script that automatically detects and corrects swapped head markers in Vicon Nexus motion capture trials. Designed for workflows where left/right head markers (`RFHD`/`LFHD` and `RBHD`/`LBHD`) are occasionally mislabeled during auto-labeling.
---
## What It Does
For each trial in a specified folder, the script:
1. Opens the trial in Vicon Nexus
2. Computes the distance from each head marker to the left and right shoulder marker clusters
3. Flags the trial if the head markers appear to be swapped (i.e., `RFHD` is closer to the left shoulder than the right)
4. If a swap is detected, swaps the full trajectories of `RFHD`↔`LFHD` and `RBHD`↔`LBHD` across all frames
5. Saves the corrected trial
Trials containing `'static'`, `'weight'`, or `'func'` in their filename are automatically skipped.
---
## Requirements
- MATLAB (any recent version)
- Vicon Nexus installed and running
- Nexus MATLAB SDK (`ViconNexus` class available on your MATLAB path)
- A Nexus session with two subjects: `body` and `bat`
---
## Setup
1. Open Vicon Nexus and load your session
2. Ensure both subjects (`body` and `bat`) are active in the session
3. Open MATLAB
4. Edit the `folder` variable at the top of `swap_markers.m` to point to your trial directory:
```matlab
folder = 'C:\path\to\your\trials\';
```
5. Run the script:
```matlab
run('swap_markers.m')
```
---
## Markers Used
| Role | Markers |
|------|---------|
| Left head | `LFHD`, `LBHD` |
| Right head | `RFHD`, `RBHD` |
| Left shoulder cluster | `LACR1`, `LACR2`, `LACR3` |
| Right shoulder cluster | `RACR1`, `RACR2`, `RACR3` |
Shoulder positions are computed as the centroid of the three ACR markers on each side. The swap check uses the region-of-interest start frame as the reference.
---
## Swap Detection Logic
A swap is flagged when:
- `RFHD` → left shoulder distance **<** `RFHD` → right shoulder distance, **AND**
- `LFHD` → right shoulder distance **<** `LFHD` → left shoulder distance
When this condition is met, all four head marker trajectories are swapped frame-by-frame using a temporary marker as an intermediate buffer.
---
## Output
- Corrected trials are saved in place (overwrites the original `.x1d`)
- The console logs each trial processed, distances computed, and whether a swap was performed
- Trials that do not need correction are closed without saving
---
## Known Limitations
- The swap check is performed at a **single frame** (region-of-interest start). If the subject is in an unusual posture at that frame, a false positive or missed swap is possible — visually inspect any flagged trials.
- The script swaps head markers only. Shoulder markers are used as reference only and are not modified.
- Requires Nexus to be open and a session to be loaded before running — the script does not launch Nexus automatically.


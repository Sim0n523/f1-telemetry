# F1 Telemetry Comparison Tool

A data analysis tool that compares two Formula 1 drivers' fastest laps using real telemetry data. Built with [FastF1](https://github.com/theOehrly/Fast-F1), the tool extracts, aligns, and visualises lap telemetry across multiple channels — providing the kind of point-by-point performance breakdown used by F1 engineers.

> Example: 2024 Monaco GP Qualifying — VER vs LEC

---

## Overview

The tool fetches official F1 timing and telemetry data, aligns both drivers onto a shared distance axis, and produces interactive multi-panel charts alongside an automated performance insights report.

The core technical challenge is **distance-axis alignment** — both drivers' raw telemetry streams are sampled at ~18 Hz but at different timestamps. The tool resamples both onto 500 evenly-spaced distance points so every comparison is spatially meaningful rather than time-offset.

**Cumulative time delta** is derived from first principles:

```
Δt = Σ (1/v₁ − 1/v₂) × Δd
```

Integrating the inverse speed difference over distance gives a running gap curve that shows exactly where and by how much one driver gains or loses time across the lap.

---

## Telemetry Channels

| Channel | Description |
|---|---|
| Speed | GPS-derived vehicle speed (km/h) |
| Throttle | Throttle pedal position (0–100%) |
| Brake | Brake pressure application (0–100%) |
| Gear | Current gear selection (1–8) |
| RPM | Engine revolutions per minute |
| Delta | Cumulative time gap between drivers (derived) |

---

## Visualisations

**Main chart** — four stacked panels sharing a common distance axis:
- Speed traces for both drivers with inter-driver fill shading
- Cumulative delta time, coloured by which driver is ahead
- Throttle application comparison
- Brake pressure comparison

**Gear & RPM chart** — step-line gear selection and RPM overlay, revealing shift points and engine usage differences.

All charts are fully interactive — hover for exact values at any track distance, zoom into specific corners, and export as standalone HTML files.

Sector boundaries are overlaid on all panels as reference markers.

---

## Setup

**Requirements:** Python 3.8+

Clone the repository and install dependencies:

```bash
git clone https://github.com/your-username/f1-telemetry.git
cd f1-telemetry
pip install -r requirements.txt
```

**Dependencies:**

```
fastf1
plotly
pandas
numpy
notebook
ipywidgets
```

---

## Usage

Open the notebook:

```bash
jupyter notebook notebooks/f1_telemetry_comparison.ipynb
```

Edit the configuration block in **Cell 2**:

```python
YEAR         = 2024
GRAND_PRIX   = 'Monaco'   # e.g. 'Monaco', 'Monza', 'Silverstone', 'Suzuka'
SESSION_TYPE = 'Q'        # 'Q' = Qualifying | 'R' = Race | 'FP1' 'FP2' 'FP3'
DRIVER_1     = 'VER'
DRIVER_2     = 'LEC'
```

Run all cells in order. The first load of a session downloads ~5–30 MB of data from the official F1 API and caches it locally — subsequent runs for the same session are instant.

Interactive HTML exports are saved to the `exports/` folder automatically.

---

## Project Structure

```
f1-telemetry/
├── notebooks/
│   └── f1_telemetry_comparison.ipynb
├── cache/          # FastF1 session cache (auto-populated, not tracked)
├── exports/        # HTML chart exports (auto-populated, not tracked)
├── requirements.txt
└── README.md
```

---

## Data Source

Session data is retrieved via the [FastF1](https://github.com/theOehrly/Fast-F1) library, which interfaces with the official Formula 1 timing API (`livetiming.formula1.com`). Data is available for all sessions from the 2018 season onwards.

---

## Screenshots

<p align="center">
  <img src="screenshots/VER vs LEC Lap Visualisation Data.png" width="400" height="300" alt="Lap Visualization Data"/>
  <img src="screenshots/VER vs LEC Gear & RPM Data.png" width="400" height="300" alt="Gear & RPM Data"/>
</p>

---

## Acknowledgements

- [FastF1](https://github.com/theOehrly/Fast-F1) by Philipp Schaefer — the library that makes F1 telemetry data accessible

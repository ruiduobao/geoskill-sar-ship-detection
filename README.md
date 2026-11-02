# SAR Ship Detection (geoskill-sar-ship-detection)

> SAR ship detection: CA/OS-CFAR constant false-alarm-rate detection + connected-component clustering to extract ship targets from single-polarization SAR intensity imagery and output GeoJSON vectors with an attribute table.

---

## 1. Overview

Automatically detects ship targets at sea from single-polarization SAR intensity imagery. Core workflow: 1. **CFAR constant false-alarm-rate detection**: estimates local clutter power from background cells outside the guard band within a sliding window and adaptively sets the threshold, maintaining a constant false-alarm rate across different sea states. - **CA-CFAR** (cell averaging): threshold = α·μ_bg, α = N·(Pfa^(−1/N) − 1) (closed-form solution for exponential clutter). - **OS-CFAR** (ordered statistics): estimates clutter from the k-th order statistic of the background, more robust to multiple targets/clutter edges. 2. **Connected-component clustering**: labels above-threshold pixels into connected components, extracts each target's area, centroid, peak intensity and bounding box, converts them to geographic coordinates and outputs GeoJSON.

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-sar-ship-detection.py --bbox 116 39 117 40 --synthetic --output-dir ./out
```

## 4. CLI Parameters

Run `python <skill>.py --help` for the full list. Common parameters:

| Parameter | Type | Description |
|---|---|---|
| `--bbox` | `float[4]` | WGS84 bounding box `min_lon min_lat max_lon max_lat` |
| `--input` | `path` | Local input file (GeoJSON/GeoTIFF/etc.) |
| `--output-dir` | `path` | Output directory (default `./output`) |
| `--synthetic` | `flag` | Use synthetic data instead of real input |
| `--quiet` | `flag` | Suppress non-essential stdout |

## 5. Input / Output

| File | Format | Description |
|---|---|---|
| `ships.geojson` | GeoJSON | Ship point targets (centroids) + attributes |
| `detection_mask.tif` | GeoTIFF | Detection mask (0/1) |
| `ship_attributes.json` | JSON | Target attribute table (area/peak/bounding box) |
| `detection_count.json` | JSON | Detection count and parameters |
| `output-manifest.json` | JSON | Run manifest |

## 6. Technical Principle

(see SKILL.md for details)

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)

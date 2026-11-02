---
name: geoskill-sar-ship-detection
description: 'SAR 船舶检测：CA/OS-CFAR 恒虚警检测 + 连通域聚类，从单极化 SAR 强度影像提取船舶目标并输出 GeoJSON 矢量与属性表'
---

# SAR船舶检测 | SAR Ship Detection

Automatically detects ship targets at sea from single-polarization SAR intensity imagery. Core workflow:

1. **CFAR constant false alarm rate detection**: within a sliding window, background cells outside the guard band estimate the local clutter power to set an adaptive threshold, maintaining a constant false alarm rate under different sea states.
   - **CA-CFAR** (cell averaging): threshold = α·μ_bg, α = N·(Pfa^(−1/N) − 1) (closed-form solution for exponential clutter).
   - **OS-CFAR** (ordered statistics): uses the k-th order statistic of the background to estimate clutter; more robust to multiple targets / clutter edges.
2. **Connected-component clustering**: labels connected components of threshold-exceeding pixels and extracts each target's area, centroid, peak intensity and bounding box, converting them to geographic coordinates and outputting GeoJSON.

## Application Scenarios / 应用场景

- Maritime vessel surveillance and illegal fishing monitoring
- Traffic-flow statistics for ports and shipping lanes
- Target screening for maritime search and rescue

## Dependencies / 依赖

```bash
pip install 'numpy' 'rasterio' 'geopandas' 'shapely' 'scipy'
```

## Usage / 使用方法

### Example 1 (synthetic data, offline)

```bash
python geoskill-sar-ship-detection.py --bbox 121.0 30.0 122.0 31.0 --synthetic --cfar ca --pfa 1e-4 --output-dir ./out
```

### Example 2 (OS-CFAR)

```bash
python geoskill-sar-ship-detection.py --bbox 121.0 30.0 122.0 31.0 --synthetic --cfar os --output-dir ./out
```

### Example 3 (real SAR imagery)

```bash
python geoskill-sar-ship-detection.py --input sar_scene.tif --cfar ca --pfa 1e-5 --output-dir ./out
```

### Example 4 (tuning window parameters)

```bash
python geoskill-sar-ship-detection.py --input sar_scene.tif --guard 3 --background 8 --output-dir ./out
```

## Output / 输出

| File | Format | Description |
|---|---|---|
| `ships.geojson` | GeoJSON | Ship point targets (centroids) + attributes |
| `detection_mask.tif` | GeoTIFF | Detection mask (0/1) |
| `ship_attributes.json` | JSON | Target attribute table (area/peak/bounding box) |
| `detection_count.json` | JSON | Detection count and parameters |
| `output-manifest.json` | JSON | Run manifest |

## Data Source / 数据源 / Source

Local single-band SAR intensity GeoTIFF, or a `--synthetic` simulated sea surface (exponential clutter + bright ship targets).

## Privacy / 隐私声明 / Privacy

- Runs offline by default; `--synthetic` mode is fully network-free.
- All processing is done locally; no user data is uploaded.

## License / License

MIT

---

> 中文完整版：需要时再读 [references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)，不要默认全量加载。

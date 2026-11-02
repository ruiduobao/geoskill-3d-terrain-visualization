---
name: geoskill-3d-terrain-visualization
description: 'Render 3D terrain from DEM and imagery with vertical exaggeration and an HTML viewer'
---

# 三维地形可视化 | 3D Terrain Visualization

Computes per-pixel normal vectors and Lambertian diffuse illumination from a DEM, overlays terrain colors to produce a lit 3D terrain map, and outputs a CSS 3D perspective viewer (draggable pitch/rotation, adjustable vertical exaggeration).

Illumination uses a diffuse reflection model driven by solar azimuth/altitude angles; vertical exaggeration amplifies the influence of elevation relative to horizontal distance via the zfactor.

## Core Algorithm / 核心算法

np.gradient computes the DEM gradient → unit normal vectors (nx,ny,nz) → dot product with the sun direction vector yields the Lambertian shade → terrain colormap × (ambient+shade).

Horizontal pixel size is computed in **meters**: EPSG:4326 (degree) inputs are automatically converted at ≈111320·cos(φ) m/degree, while projected-coordinate inputs are first reprojected to WGS84; zfactor is a pure vertical exaggeration factor (consistent with the GDAL gdaldem `-z` / ESRI z_factor convention). NoData pixels are excluded from the gradient and statistics and are marked as nodata in the output; bbox and parameter ranges are validated (invalid input exits with code 6).

## Dependencies / 依赖

```bash
pip install numpy rasterio scipy matplotlib geopandas shapely pillow
```

## Usage / 使用方法

### Example 1 (Synthetic Data, Offline)

```bash
python geoskill-3d-terrain-visualization.py --bbox 116.0 39.0 117.0 40.0 --synthetic --output-dir ./out
```

### Example 2 (Custom Lighting)

```bash
python geoskill-3d-terrain-visualization.py --input dem.tif --azimuth 270 --altitude 35 --exaggeration 3
```

### Example 3 (Low Ambient Light for Stronger Relief)

```bash
python geoskill-3d-terrain-visualization.py --input dem.tif --ambient 0.05
```

### Example 4 (Synthetic Mode with Exaggeration)

```bash
python geoskill-3d-terrain-visualization.py --bbox 116 39 117 40 --synthetic --exaggeration 4
```

### 示例 5（自定义 cellsize）

```bash
python geoskill-3d-terrain-visualization.py --input dem.tif --cellsize 30
```

## Output / 输出

| File | Format | Description |
|---|---|---|
| `terrain_3d.html` | HTML | CSS 3D perspective viewer (primary output) |
| `shaded_relief.tif` | GeoTIFF | Illumination intensity raster [0,1] (verifiable output) |
| `terrain_3d.json` | JSON | Illumination/exaggeration/extent metadata |

Each run also produces `output-manifest.json` (run manifest).

## Data Source / 数据源 / Source

Local GeoTIFF / vector files; `--synthetic` mode generates physically consistent simulated data, fully offline.

## Privacy / 隐私声明 / Privacy

- Runs offline by default; `--synthetic` mode requires no network at all.
- All processing is done locally; user data is never uploaded.

## License / License

MIT

---

> 中文完整版：需要时再读 [references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)，不要默认全量加载。

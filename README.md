# 3D Terrain Visualization (geoskill-3d-terrain-visualization)

> Render 3D terrain from DEM and imagery with vertical exaggeration and an HTML viewer

---

## 1. Overview

Computes per-pixel normal vectors and Lambertian diffuse shading from a DEM, overlays terrain colors to produce a lit 3D terrain map, and outputs a CSS 3D perspective viewer (drag to tilt/rotate, adjust vertical exaggeration). Lighting uses a diffuse reflection model driven by solar azimuth/elevation angles; vertical exaggeration amplifies the effect of elevation relative to horizontal distance via the zfactor.

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-3d-terrain-visualization.py --bbox 116 39 117 40 --synthetic --output-dir ./out
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
| `terrain_3d.html` | HTML | CSS 3D perspective viewer (primary output) |
| `shaded_relief.tif` | GeoTIFF | Illumination intensity raster [0,1] (verifiable output) |
| `terrain_3d.json` | JSON | Lighting/exaggeration/extent metadata |

Each run also produces `output-manifest.json` (run manifest).


## 6. Technical Principle

np.gradient computes the DEM gradient → unit normal vectors (nx,ny,nz) → dot product with the solar direction vector yields Lambertian shade → terrain colormap × (ambient+shade).

Horizontal pixel size is computed in **meters**: EPSG:4326 (degree) inputs are automatically converted at ≈111320·cos(φ) m/degree, while projected-coordinate inputs are first reprojected to WGS84; zfactor is a pure vertical exaggeration coefficient (consistent with GDAL gdaldem `-z` / ESRI z_factor conventions). NoData pixels do not participate in gradient or statistics computation and are marked as nodata in the output; bbox and parameter value ranges are validated (invalid input exits with code 6).

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)

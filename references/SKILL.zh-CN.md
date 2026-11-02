# 三维地形可视化 | 3D Terrain Visualization

从 DEM 计算逐像元法向量与 Lambertian 漫反射光照，叠加 terrain 色彩生成带光照的三维地形图，并输出一个 CSS 3D 透视查看器（可拖动俯仰/旋转、调节垂直夸张）。

光照采用太阳方位角/高度角驱动的漫反射模型；垂直夸张通过 zfactor 放大高程相对水平距离的影响。

## 核心算法

np.gradient 求 DEM 梯度 → 单位法向量 (nx,ny,nz) → 与太阳方向向量点积得 Lambertian shade → terrain colormap × (ambient+shade)。

水平像元尺寸按**米**计算：EPSG:4326（度）输入自动按 ≈111320·cos(φ) m/度换算，投影坐标输入先重投影到 WGS84；zfactor 为纯垂直夸张系数（与 GDAL gdaldem `-z` / ESRI z_factor 约定一致）。NoData 像元不参与梯度与统计，输出中标记为 nodata；bbox 与参数值域均有校验（非法输入退出码 6）。

## 依赖

```bash
pip install numpy rasterio scipy matplotlib geopandas shapely pillow
```

## 使用方法

### 示例 1（合成数据，离线）

```bash
python geoskill-3d-terrain-visualization.py --bbox 116.0 39.0 117.0 40.0 --synthetic --output-dir ./out
```

### 示例 2（自定义光照）

```bash
python geoskill-3d-terrain-visualization.py --input dem.tif --azimuth 270 --altitude 35 --exaggeration 3
```

### 示例 3（低环境光更立体）

```bash
python geoskill-3d-terrain-visualization.py --input dem.tif --ambient 0.05
```

### 示例 4（合成模式指定夸张）

```bash
python geoskill-3d-terrain-visualization.py --bbox 116 39 117 40 --synthetic --exaggeration 4
```

### 示例 5（自定义 cellsize）

```bash
python geoskill-3d-terrain-visualization.py --input dem.tif --cellsize 30
```

## 输出

| 文件 | 格式 | 说明 |
|---|---|---|
| `terrain_3d.html` | HTML | CSS 3D 透视查看器（主产物） |
| `shaded_relief.tif` | GeoTIFF | 光照强度栅格 [0,1]（可验证产物） |
| `terrain_3d.json` | JSON | 光照/夸张/范围元数据 |

每次运行还会产出 `output-manifest.json`（运行清单）。

## 数据源 / Source

本地 GeoTIFF / 矢量文件；`--synthetic` 模式生成物理一致的模拟数据，完全离线。

## 隐私声明 / Privacy

- 默认离线运行，`--synthetic` 模式完全无网络。
- 所有处理在本地完成，不上传用户数据。

## License

MIT

# raster-format-utils

A collection of Python utilities for converting and managing geospatial raster data formats. This lightweight toolkit demonstrates efficient workflows for converting raster data between common GIS formats using Rasterio and GDAL, making it easy to integrate into geospatial data processing pipelines.

## Features

- **Convert raster data types** (e.g., 8-bit to 16-bit) using Rasterio
- **Export rasters to different formats** (e.g., GeoTIFF to Idrisi RST) via GDAL
- **Simple, script-based interface** suitable for batch processing or integration into larger workflows

## Example: 8-bit to 16-bit GeoTIFF, then to Idrisi RST

This example script reads an 8-bit GeoTIFF, converts it to a 16-bit GeoTIFF, and then exports it as an Idrisi RST raster using GDAL.

```python
import rasterio
import numpy as np
import subprocess

# Input and output file paths
in_fp = r"E:\DEMs\Headwaters_Boyer_River\KFactor_8_bit.tif"
out_fp = r"E:\DEMs\Headwaters_Boyer_River\KFactor_16bit.tif"
rst_fp = r"E:\DEMs\Headwaters_Boyer_River\KFactor_16bit.rst"

# Step 1: Convert 8-bit to 16-bit GeoTIFF
with rasterio.open(in_fp) as src:
    data = src.read(1)
    profile = src.profile.copy()
    profile.update(dtype='int16', count=1, compress='lzw')
    data16 = data.astype(np.int16)
    with rasterio.open(out_fp, 'w', **profile) as dst:
        dst.write(data16, 1)
print("GeoTIFF 16-bit raster saved:", out_fp)

# Step 2: Convert GeoTIFF to Idrisi RST using GDAL
subprocess.run([
    "gdal_translate",
    "-of", "RST",
    out_fp,
    rst_fp
])
print("RST raster saved:", rst_fp)

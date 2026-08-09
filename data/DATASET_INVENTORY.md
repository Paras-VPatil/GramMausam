# Dataset Inventory — GramMausam Project

This document provides a comprehensive audit of all datasets currently available in the project workspace. 

> [!NOTE]
> This inventory was compiled dynamically by inspecting raw XML metadata and raw binary headers directly, bypassing the absence of NetCDF and JPEG2000 libraries in the active Conda environment.

---

## 📊 Summary Table

| Dataset Name | Filename | Format | Size (MB) | CRS | Spatial Resolution | Variables / Layers | Complete & Readable |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **GADM India Boundaries** | `gadm41_IND.gpkg` | GeoPackage | 49.58 MB | EPSG:4326 | Vector | ADM_0 to ADM_3 layers | **Yes** (Fiona/GeoPandas) |
| **CDS ERA5 (Accumulated)** | `115ca30de3823.../data_stream-oper_stepType-accum.nc` | NetCDF-4 | 0.02 MB | EPSG:4326 | 0.25° (~28 km) | tp (precipitation) | **Incomplete/Missing** (no python parser, size is too small) |
| **CDS ERA5 (Instantaneous)** | `115ca30de3823.../data_stream-oper_stepType-instant.nc` | NetCDF-4 | 0.06 MB | EPSG:4326 | 0.25° (~28 km) | sp, t2m, d2m_C, u10, v10 | **Incomplete/Missing** (no python parser, size is too small) |
| **Sentinel-2 Tile T43QDA** | `S2A_MSIL2A_...T43QDA_...SAFE.zip` | ZIP (SAFE) | 1,141.89 MB | EPSG:32643 | 10m / 20m / 60m | B01-B12, AOT, WVP, SCL, TCI | **Yes** (Metadata read OK; band images need OpenJPEG driver) |
| **Sentinel-2 Tile T43QDV** | `S2A_MSIL2A_...T43QDV_...SAFE.zip` | ZIP (SAFE) | 1,137.23 MB | EPSG:32643 | 10m / 20m / 60m | B01-B12, AOT, WVP, SCL, TCI | **Yes** (Metadata read OK; band images need OpenJPEG driver) |

---

## 🔍 Detailed Dataset Audits

### 1. GADM 4.1 India Boundaries
* **Filename:** `gadm41_IND.gpkg`
* **Format:** GeoPackage (.gpkg)
* **File Size:** 51,994,624 bytes (49.58 MB)
* **Spatial Coverage:** India national extent (Lon: 68.186249 to 97.415161 E, Lat: 6.754256 to 35.501331 N)
* **Temporal Coverage:** Static administrative boundaries representing 2022.
* **CRS / EPSG:** EPSG:4326 (WGS 84, Geographic 2D)
* **Spatial Resolution:** Vector (n/a)
* **Temporal Resolution:** Static snapshot
* **Variables / Layers / Attributes:**
  * `ADM_ADM_0` (Country level, 6 features): `GID_0` (IND), `COUNTRY` (India)
  * `ADM_ADM_1` (State/UT level, 41 features): State name field is `NAME_1`, code field `GID_1`
  * `ADM_ADM_2` (District level, 676 features): District name field is `NAME_2`, code field `GID_2`
  * `ADM_ADM_3` (Sub-district/Tehsil level, 2347 features): Sub-district name field is `NAME_3`, code field `GID_3`
* **NoData Values:** Missing attributes default to `'NA'` or `'n.a.'`
* **Coordinate/Boundary Info:** National bounding box [68.186249, 6.754256, 97.415161, 35.501331]
* **Readability Status:** **Fully Complete and Readable.** Layer headers, CRS, schemas, and geometry features are fully accessible in python via Fiona and GeoPandas.

---

### 2. Climate Data Store (CDS) ERA5 — Accumulated Precipitation
* **Filename:** `115ca30de3823b1ccab0168e272a7a71/data_stream-oper_stepType-accum.nc`
* **Format:** NetCDF-4 (HDF5 format)
* **File Size:** 25,671 bytes (25.07 KB)
* **Spatial Coverage:** Western Maharashtra bounding box (Lon: 73.5 to 76.5 E, Lat: 17.0 to 19.5 N)
* **Temporal Coverage:** Downloaded 2026-08-09. Contains only a very short time range (likely 24 hours).
* **CRS / EPSG:** EPSG:4326 (WGS 84, Geographic 2D)
* **Spatial Resolution:** 0.25° x 0.25° (~28 km grid)
* **Temporal Resolution:** Hourly
* **Variables / Attributes:**
  * Coordinates: `latitude`, `longitude`, `time`, `valid_time`
  * Variables: `tp` (Total Precipitation)
  * Units: Meters (`m`)
* **NoData Values:** `9999` (as defined in HDF5 binary attributes `GRIB_missingValue`)
* **Coordinate/Boundary Info:** Grid spans Lat [17.0, 19.5], Lon [73.5, 76.5] (13 longitude columns x 11 latitude rows = 143 grid cells).
* **Readability Status:** **Incomplete/Missing.** Structurally valid as an HDF5 container, but **not readable** in the active python environment because `netcdf4`, `xarray`, and `h5py` are missing and GDAL lacks the NetCDF driver. Furthermore, the file is **incomplete**; a size of 25 KB is physically impossible for the 2018–2024 hourly time series required by the project roadmap (which should be ~14 MB). It appears to contain only a 24-hour test slice.

---

### 3. Climate Data Store (CDS) ERA5 — Instantaneous Variables
* **Filename:** `115ca30de3823b1ccab0168e272a7a71/data_stream-oper_stepType-instant.nc`
* **Format:** NetCDF-4 (HDF5 format)
* **File Size:** 67,514 bytes (65.93 KB)
* **Spatial Coverage:** Western Maharashtra bounding box (Lon: 73.5 to 76.5 E, Lat: 17.0 to 19.5 N)
* **Temporal Coverage:** Downloaded 2026-08-09. Contains only a very short time range (likely 24 hours).
* **CRS / EPSG:** EPSG:4326 (WGS 84, Geographic 2D)
* **Spatial Resolution:** 0.25° x 0.25° (~28 km grid)
* **Temporal Resolution:** Hourly
* **Variables / Attributes:**
  * Coordinates: `latitude`, `longitude`, `time`, `valid_time`
  * Variables: `sp` (Surface pressure, Pa), `t2m` (2m temperature, K), `d2m_C` (2m dewpoint temperature, K), `u10` (10m U wind component, m/s), `v10` (10m V wind component, m/s)
* **NoData Values:** `9999`
* **Coordinate/Boundary Info:** Grid spans Lat [17.0, 19.5], Lon [73.5, 76.5] (13 longitude columns x 11 latitude rows = 143 grid cells).
* **Readability Status:** **Incomplete/Missing.** Structurally valid as HDF5, but **not readable** in Python due to the same missing packages and missing GDAL NetCDF driver. The file is **incomplete**; a size of 67 KB cannot store the 2018–2024 hourly archive. It contains only a 24-hour test slice.

---

### 4. Sentinel-2 Tile T43QDA
* **Filename:** `S2A_MSIL2A_20160106T053222_N0500_R105_T43QDA_20231009T230514.SAFE.zip`
* **Format:** ZIP archive containing Sentinel-2 SAFE folder structure.
* **File Size:** 1,197,352,914 bytes (1.11 GB)
* **Spatial Coverage:** UTM Zone 43N tile T43QDA.
  * Bounding Box (Lon: 74.049591 to 75.092727 E, Lat: 17.997827 to 18.992499 N)
* **Temporal Coverage:** Single acquisition date/time: **2016-01-06T05:32:22.029Z**
* **CRS / EPSG:** EPSG:32643 (WGS 84 / UTM zone 43N)
* **Spatial Resolution:** 10m, 20m, and 60m depending on the bands.
* **Temporal Resolution:** Revisit snapshot (5-day revisit for the constellation).
* **Variables / Bands:**
  * 10m Bands: `B02` (Blue), `B03` (Green), `B04` (Red), `B08` (NIR), `TCI` (True Color Image)
  * 20m Bands: `B05` (Red Edge 1), `B06` (Red Edge 2), `B07` (Red Edge 3), `B8A` (Narrow NIR), `B11` (SWIR 1), `B12` (SWIR 2), `AOT` (Aerosol), `WVP` (Water Vapour)
  * 60m Bands: `B01` (Coastal), `B09` (Water Vapour)
* **Cloud Cover Info:** **0.000537%** (Virtually cloud-free)
* **NoData Values:** 0
* **Coordinate/Boundary Info:** UTM bbox: X [399960, 509760], Y [1990200, 2100000]
* **Readability Status:** **Complete but restricted.** The ZIP archive is intact and the XML files are fully readable. However, the image files themselves (.jp2 format) are **not readable** in Python using `rasterio` or GDAL because the environment lacks the `gdal_JP2OpenJPEG.dll` plugin.

---

### 5. Sentinel-2 Tile T43QDV
* **Filename:** `S2A_MSIL2A_20160106T053222_N0500_R105_T43QDV_20231009T230514.SAFE.zip`
* **Format:** ZIP archive containing Sentinel-2 SAFE folder structure.
* **File Size:** 1,192,470,584 bytes (1.11 GB)
* **Spatial Coverage:** UTM Zone 43N tile T43QDV.
  * Bounding Box (Lon: 74.054573 to 75.092241 E, Lat: 17.094405 to 18.089048 N)
* **Temporal Coverage:** Single acquisition date/time: **2016-01-06T05:32:22.029Z**
* **CRS / EPSG:** EPSG:32643 (WGS 84 / UTM zone 43N)
* **Spatial Resolution:** 10m, 20m, and 60m depending on the bands.
* **Temporal Resolution:** Revisit snapshot.
* **Variables / Bands:** (Same as T43QDA: B01-B12, AOT, WVP, SCL, TCI)
* **Cloud Cover Info:** **1.054987%** (Extremely clear, < 1.1% clouds)
* **NoData Values:** 0
* **Coordinate/Boundary Info:** UTM bbox: X [399960, 509760], Y [1890240, 2000040]
* **Readability Status:** **Complete but restricted.** The ZIP archive is intact and XML files are readable. The JP2 band image files cannot be read in Python due to the missing GDAL OpenJPEG driver.

---

## ⚠️ Important Missing Information & Gaps

1. **ERA5 Reanalysis Time Series Data (2018–2024) is Missing:**
   * The active files `data_stream-oper_stepType-accum.nc` and `data_stream-oper_stepType-instant.nc` are extremely small (25 KB and 67 KB). These files are only small test slices/placeholders containing approximately 24 hours of data. The required 6-year climate time series data has not been downloaded or is missing.
2. **Missing Python NetCDF/HDF5 Parsing Libraries:**
   * The active Conda environment lacks `netcdf4`, `xarray`, and `h5py`. Python scripts cannot read or process NetCDF files in this environment.
3. **Missing GDAL NetCDF Driver:**
   * The GDAL installation (`libgdal-core`) does not have the NetCDF driver compiled in, preventing standard raster tools like `rasterio` or `gdalinfo` from reading `.nc` files.
4. **Missing GDAL JP2OpenJPEG Driver:**
   * The GDAL installation lacks the OpenJPEG driver, meaning Python cannot open or process the Sentinel-2 `.jp2` band files.
5. **Soil and Elevation Datasets are Missing:**
   * Soil (SoilGrids 250m) and DEM (Copernicus GLO-30 / SRTM 30m) datasets described in the roadmap are completely missing from the workspace.

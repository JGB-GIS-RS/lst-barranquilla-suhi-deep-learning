# Data sources

This document describes the satellite and climate-radiative data sources used in
the Barranquilla LST/SUHI study, together with the principal criteria applied to
construct the public input-data record.

## 1. Landsat satellite data

The study uses Landsat 8 and Landsat 9 Collection 2 Level-2 Science Products
acquired by the Operational Land Imager/Thermal Infrared Sensor (OLI/TIRS) and
the Operational Land Imager 2/Thermal Infrared Sensor 2 (OLI-2/TIRS-2),
respectively.

The products were accessed through Google Earth Engine using:

```text
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

These collections provide atmospherically corrected surface reflectance, an
operational land surface temperature product, and pixel-level quality-assurance
bands. Restricting the analysis to Landsat 8/9 preserves sensor consistency across
the 2013-2025 operational period.

The final scene inventory contains the acquisitions retained for WRS-2 path/row
009/052. Lightweight scene-level and temporal-summary tables are included in
`data/scene_inventory/`.

## 2. Study domain and spatial reference

The study domain covers the Barranquilla Metropolitan Area and its surrounding
urban, peri-urban, coastal, fluvial, vegetated, and exposed-soil environments in
northern Colombia.

```text
Landsat WRS-2 path/row: 009/052
Projected coordinate reference system: EPSG:32618
Nominal spatial resolution: 30 m
```

All annual Landsat-derived products were harmonized to a common canonical grid
with fixed extent, resolution, coordinate reference system, spatial transform,
and NoData convention before temporal tensor construction.

## 3. Temporal coverage

The operational Landsat period is:

```text
2013-2025
```

The year 2026 is excluded because it does not represent a complete annual period
comparable with the preceding years.

The resulting T3 modeling period contains target years 2016-2025:

```text
Training targets:   2016-2019
Validation targets: 2020-2022
Test targets:       2023-2025
```

Each scene inventory record documents the principal acquisition and processing
metadata required for temporal traceability, including scene identifier, sensor,
acquisition date, year, month, day of year, WRS path/row, cloud-cover metadata,
collection category, processing level, and spacecraft identifier.

## 4. Landsat-derived variables

The target variable is annual land surface temperature (LST), expressed in degrees
Celsius before train-only normalization.

The explanatory variables are six annual spectral indices derived from Landsat
surface reflectance:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

Their methodological roles are:

- `NDVI`: vegetation vigor and photosynthetically active cover;
- `NDMI`: vegetation/canopy moisture and water-stress proxy;
- `NDBI`: built-up and impervious-surface response;
- `UI`: urban spectral response emphasizing SWIR2;
- `SAVI`: vegetation response adjusted for soil background;
- `BSI`: bare-soil and exposed-surface response.

The NIR-SWIR1 index is reported as `NDMI`, not `NDWI`, to distinguish it from the
Green-NIR water index commonly used for open-water detection.

Surface-reflectance bands and `ST_B10` were converted to physical units using the
official Landsat Collection 2 scale factors and offsets before annual product
generation.

## 5. Annual product construction

For each year and variable, valid observations were aggregated using a pixel-wise
annual median. Pixels with fewer than two valid observations in a year were
excluded from the accepted annual composite.

Annual products were generated for:

```text
LST, NDVI, NDMI, NDBI, UI, SAVI, BSI
```

Observation-count layers were also produced as diagnostics of annual data
availability. These full-resolution raster products are not stored in the public
repository.

## 6. Quality assurance and valid-observation domain

The Landsat valid-observation domain combines atmospheric, radiometric, geometric,
and data-availability criteria.

The quality-control workflow excludes observations flagged as:

- fill;
- dilated cloud;
- cloud;
- cloud shadow;
- snow or ice;
- radiometrically saturated.

The atmospheric mask is derived from `QA_PIXEL`, and radiometric saturation is
screened using `QA_RADSAT`. Final validity also requires effective availability of
surface-reflectance and LST values after clipping, reprojection, resampling, and
grid harmonization.

Open-water and structurally non-land areas are excluded from the terrestrial
modeling domain to avoid mixing land and water thermal regimes. The exact masking
and domain-intersection logic is implemented in the preprocessing and
quality-control notebooks.

## 7. NASA POWER climate-radiative data

Daily regional climate data were obtained through the NASA POWER Daily API for
the centroid of the modeling domain. These data were used as annual regional
descriptors and were not treated as spatially distributed fields at Landsat
resolution.

The source series considered include:

- 2-m air temperature;
- surface shortwave solar radiation under all-sky conditions;
- corrected total precipitation;
- relative humidity;
- wind speed.

Annual summaries and interannual differences were derived for 2013-2025. The
final M5 configuration retains two target-year descriptors:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These variables represent the annual change between the target year `Y` and
`Y-1`. They are replicated spatially within each patch only to ensure tensor
compatibility; this operation does not imply 30-m intra-urban climate variability.

The public folder name `climate_forcings` is retained for consistency with the
notebooks and repository history. Scientifically, the retained variables are
interpreted as regional climate-radiative descriptors.

## 8. Lightweight public data products

The `data/` directory contains compact products that support methodological
inspection and traceability:

```text
data/
├── scene_inventory/
├── quality_control/
├── normalization/
└── climate_forcings/
```

These folders contain selected CSV, JSON, metadata, audit, and summary products
associated with scene availability, valid-pixel diagnostics, train-only
normalization, and NASA POWER descriptor construction.

The public tables do not replace the full-resolution data used during model
training and evaluation.

## 9. Data intentionally excluded

The following large-volume products are not stored in GitHub:

- original Landsat scenes;
- annual full-resolution GeoTIFF products;
- aligned raster stacks and multi-year cubes;
- tensor datasets;
- extracted patch datasets;
- trained model weights and checkpoints;
- full-resolution prediction and residual maps;
- temporary and intermediate processing outputs.

These exclusions are deliberate and define the storage boundary of the public
repository.

## 10. Reproducibility scope

The repository supports methodological inspection, traceability, and partial
reproducibility. Complete regeneration of the analysis requires access to the
original Landsat and NASA POWER data, the large intermediate products, adequate
storage, and suitable computational resources.

The corresponding implementation is documented in:

```text
notebooks/01_scene_inventory_landsat_8_9.ipynb
notebooks/02_preprocessing_lst_indices.ipynb
notebooks/03_quality_control.ipynb
notebooks/04_train_only_normalization.ipynb
notebooks/05_climate_forcing_integration.ipynb
docs/preprocessing.md
docs/workflow.md
```

The notebooks and manuscript remain the authoritative descriptions of the
implemented data-processing workflow.

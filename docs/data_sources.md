# Data sources

This document describes the primary data sources used in the study and the criteria for reconstructing the input dataset.

## 1. Satellite data

The study uses Landsat 8/9 Collection 2 Level-2 products.

These products provide surface reflectance bands and land surface temperature information suitable for remote sensing analysis of urban thermal patterns.

The satellite collections considered in the workflow are:

```
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

The workflow is restricted to Landsat 8 and Landsat 9 to maintain sensor consistency during the operational period of the study.

## 2. Study area

The study area corresponds to Barranquilla, Colombia, including its urban and peri-urban surroundings.

The Landsat WRS-2 reference used for the scene inventory is:

```
Path: 9
Row: 52
```

All satellite-derived variables must be clipped, masked, and spatially harmonized to the study area before tensor construction and modeling.

## 3. Temporal coverage

The operational period of the workflow is:

```
2013–2025
```

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

Each retained image should be documented with, at minimum:

* scene identifier;
* sensor;
* acquisition date;
* year;
* month;
* day of year;
* WRS path;
* WRS row;
* cloud cover;
* land cloud cover;
* collection category;
* processing level;
* spacecraft identifier.

## 4. Input variables

The target variable is land surface temperature.

The explanatory variables are spectral indices derived from Landsat surface reflectance bands. These indices are used to characterize vegetation condition, moisture, built-up surfaces, bare soil response, and urban spectral behavior.

Expected variables include:

* LST;
* NDVI;
* NDMI or NDWI, according to the final manuscript terminology;
* NDBI;
* UI;
* SAVI;
* BSI, if retained in the final model configuration.

The final variable set must match the manuscript, notebooks, and configuration files.

## 5. Quality masking

Quality masking must be applied consistently before computing spectral indices, constructing temporal datasets, or training models.

The masking procedure should remove, at minimum:

* fill pixels;
* clouds;
* cloud shadows;
* cirrus contamination, when available;
* snow or invalid pixels, when applicable;
* pixels outside the valid study area.

For Landsat Collection 2 Level-2 products, the masking procedure should be based on the QA bands provided with each scene.

The exact bitwise masking implementation must be reported in the preprocessing notebook or source code.

## 6. Scene inventory products

The repository may include lightweight tabular products derived from the Landsat 8/9 scene inventory.

These products are intended to improve transparency and allow reviewers to inspect the temporal availability of input scenes without downloading raster data.

Expected inventory products include:

* scene-level inventory table;
* annual scene count table;
* monthly scene count table.

These files should be stored in:

```
data/scene_inventory/
```

## 7. Data not stored in this repository

Large geospatial files are not stored in GitHub.

This includes:

* original Landsat scenes;
* intermediate raster stacks;
* full-resolution GeoTIFF files;
* temporal tensors;
* extracted patches;
* trained model checkpoints;
* large model outputs.

These files should be reconstructed from the documented workflow or stored externally using an appropriate storage system.

## 8. Reproducibility strategy

The repository provides code, documentation, configuration files, notebooks, and lightweight tabular products to support methodological inspection and partial reproducibility.

Full workflow reproduction requires access to the original satellite products, preprocessing outputs, and adequate computational resources.

## 9. Pending updates

This document must be updated if the final manuscript changes the temporal coverage, input variable set, masking criteria, or modeling strategy.

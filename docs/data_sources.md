# Data sources

This document describes the primary data sources used in the study and the criteria for reconstructing the input dataset.

## 1. Satellite data

The study is based on Landsat Collection 2 Level-2 products.

These products provide atmospherically corrected surface reflectance bands and land surface temperature products suitable for long-term remote sensing analysis.

The Landsat sensors considered in the workflow may include:

- Landsat 8 Operational Land Imager / Thermal Infrared Sensor (OLI/TIRS);
- Landsat 9 Operational Land Imager-2 / Thermal Infrared Sensor-2 (OLI-2/TIRS-2).

## 2. Study area

The study area corresponds to Barranquilla, Colombia, including its urban and peri-urban surroundings.

All satellite-derived variables must be clipped and spatially harmonized to the study area before tensor construction.

## 3. Temporal coverage

The temporal coverage must be defined according to the final manuscript and the available Landsat scene inventory.

Each retained image should be documented with:

- acquisition date;
- Landsat mission and sensor;
- scene identifier;
- path/row;
- cloud cover metadata;
- processing level;
- inclusion or exclusion status;
- exclusion reason, when applicable.

## 4. Input variables

The target variable is land surface temperature.

The explanatory variables are spectral indices derived from Landsat surface reflectance bands. These indices are used to characterize vegetation, moisture, built-up surfaces, and urban spectral response.

Expected variables include:

- LST;
- NDVI;
- NDWI;
- NDBI;
- UI;
- SAVI.

The final variable set must match the variables reported in the manuscript.

## 5. Quality masking

Quality masking must be applied consistently before temporal stacking.

The masking procedure should remove, at minimum:

- clouds;
- cloud shadows;
- cirrus contamination, when available;
- snow or invalid pixels, when applicable;
- fill values;
- pixels outside the valid study area.

The exact QA bands and bit masks used must be reported in the preprocessing notebook or source code.

## 6. Data not stored in this repository

Large geospatial files are not stored in GitHub.

This includes:

- original Landsat scenes;
- intermediate raster stacks;
- full-resolution GeoTIFF files;
- temporal tensors;
- extracted patches;
- trained model checkpoints;
- large model outputs.

These files should be stored externally using an appropriate storage system.

## 7. Reproducibility strategy

The repository provides code and documentation to reconstruct the dataset from the original satellite products.

A lightweight sample dataset may be included for testing the computational pipeline, but it should not be interpreted as the full dataset used in the manuscript.

## 8. Pending updates

This document must be updated once the final scene inventory, temporal coverage, and input variable set are fixed.

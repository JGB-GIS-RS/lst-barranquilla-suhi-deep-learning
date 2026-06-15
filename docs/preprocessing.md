# Preprocessing

This document describes the preprocessing workflow used to prepare Landsat-derived variables for surface urban heat modeling in Barranquilla, Colombia.

## 1. General preprocessing objective

The objective of preprocessing is to generate spatially aligned, quality-controlled raster layers suitable for multi-temporal dataset construction and deep learning model training.

The preprocessing workflow produces two main groups of variables:

* land surface temperature;
* spectral indices derived from Landsat surface reflectance bands.

## 2. Satellite products

The workflow is based on Landsat 8/9 Collection 2 Level-2 products.

The satellite collections considered in the workflow are:

```
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

These products provide:

* surface reflectance bands;
* land surface temperature products;
* quality assessment bands.

Using Level-2 products reduces the need for manually implementing atmospheric correction, although quality masking, spatial harmonization, and consistency checks are still required.

## 3. Spatial and temporal scope

The study area corresponds to Barranquilla, Colombia, including its urban and peri-urban surroundings.

The Landsat WRS-2 reference used for the scene inventory is:

```
Path: 9
Row: 52
```

The operational period of the workflow is:

```
2013–2025
```

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

## 4. Spatial subset and grid consistency

All scenes must be clipped to the Barranquilla study area and its surrounding urban and peri-urban environment.

All output rasters must share:

* the same coordinate reference system;
* the same spatial resolution;
* the same grid alignment;
* the same spatial extent;
* the same valid-pixel mask logic.

Spatial misalignment between dates can introduce artificial temporal change and must be corrected before temporal stacking, tensor construction, or patch extraction.

## 5. Quality masking

Quality masking is applied before computing spectral indices and before constructing temporal datasets.

The masking procedure should remove pixels affected by:

* fill values;
* clouds;
* cloud shadows;
* cirrus contamination, when available;
* invalid observations;
* saturated or otherwise unreliable pixels, when relevant;
* pixels outside the valid study area.

For Landsat Collection 2 Level-2 products, the masking procedure should be based on the QA bands provided with each scene, especially `QA_PIXEL`.

The exact bitwise implementation must be reported in the preprocessing notebook or source code.

## 6. Land surface temperature

Land surface temperature is used as the target variable of the modeling workflow.

LST is extracted from the Landsat Collection 2 Level-2 thermal product and converted to physical temperature units according to the scale factors and offsets defined for the product.

All LST layers must be checked for:

* valid physical range;
* missing values;
* spatial artifacts;
* abnormal scene-level statistics;
* consistency across Landsat 8 and Landsat 9;
* consistency across years in the 2013–2025 operational period.

## 7. Surface reflectance and spectral indices

Spectral indices are computed from surface reflectance bands after masking invalid pixels.

The expected spectral indices include:

* NDVI;
* NDMI or NDWI, according to the final manuscript terminology;
* NDBI;
* UI;
* SAVI;
* BSI, if retained in the final model configuration.

The final list of indices must match the manuscript, notebooks, and model input configuration.

## 8. Cross-sensor consistency

Because the workflow uses Landsat 8 and Landsat 9, cross-sensor consistency must be considered.

The workflow must account for differences or residual inconsistencies between:

* Landsat 8 OLI/TIRS;
* Landsat 9 OLI-2/TIRS-2.

Potential issues include differences in spectral response, thermal calibration, acquisition conditions, and scene availability.

The use of Landsat 8/9 only helps reduce cross-sensor heterogeneity compared with workflows that combine older Landsat missions.

## 9. Normalization

Normalization parameters must be estimated using training data only.

This is required to avoid information leakage between training, validation, and test datasets.

The recommended strategy is:

* robust min-max scaling for spectral indices;
* standardization of LST using training-period statistics.

All normalization parameters must be stored or documented to allow reproducible inference.

## 10. Output products

The preprocessing stage should produce spatially consistent raster layers for each retained acquisition date.

Expected outputs include:

* masked LST raster;
* masked spectral index rasters;
* valid-pixel mask;
* metadata table;
* quality-control summary.

Large preprocessing outputs are not stored in this GitHub repository.

Only lightweight tabular products may be included when they improve transparency and reproducibility.

## 11. Quality control

Each preprocessed scene should be evaluated using summary diagnostics, including:

* valid-pixel percentage;
* minimum, maximum, mean, and standard deviation of LST;
* spectral index ranges;
* spatial distribution of valid and invalid pixels;
* visual inspection of masks;
* detection of anomalous scenes.

Scenes failing quality-control criteria should be excluded or explicitly flagged.

## 12. Reproducibility notes

The preprocessing workflow must be implemented in executable notebooks and reusable source-code modules.

The corresponding preprocessing notebook should document:

* input scene inventory;
* masking logic;
* LST scaling and unit conversion;
* spectral index formulas;
* output paths;
* quality-control criteria;
* excluded scenes, when applicable.

Full preprocessing reproduction requires access to the original Landsat 8/9 Collection 2 Level-2 products and adequate geospatial processing resources.

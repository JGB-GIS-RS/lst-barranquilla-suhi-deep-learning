# Preprocessing

This document describes the preprocessing workflow used to prepare Landsat-derived variables for surface urban heat modeling in Barranquilla, Colombia.

## 1. General preprocessing objective

The objective of preprocessing is to generate spatially aligned, quality-controlled raster layers suitable for multi-temporal tensor construction and deep learning model training.

The preprocessing workflow produces two main groups of variables:

- land surface temperature;
- spectral indices derived from surface reflectance bands.

## 2. Landsat Collection 2 Level-2 products

The workflow is based on Landsat Collection 2 Level-2 products.

These products provide:

- surface reflectance bands;
- land surface temperature products;
- quality assessment bands.

Using Level-2 products reduces the need for manually implementing atmospheric correction, although additional masking and harmonization are still required.

## 3. Spatial subset

All scenes are clipped to the Barranquilla study area and its surrounding urban and peri-urban environment.

All output rasters must share:

- the same coordinate reference system;
- the same spatial resolution;
- the same grid alignment;
- the same spatial extent;
- the same valid-pixel mask logic.

Spatial misalignment between dates can introduce artificial temporal change and must be corrected before tensor construction.

## 4. Quality masking

Quality masking is applied before computing indices and before temporal stacking.

The masking procedure should remove pixels affected by:

- cloud;
- cloud shadow;
- cirrus, when available;
- fill values;
- invalid observations;
- saturated or otherwise unreliable pixels, when relevant.

For Landsat Collection 2 Level-2 products, the masking procedure should be based on the QA bands provided with each scene.

The exact bitwise implementation must be reported in the preprocessing notebook or source code.

## 5. Land surface temperature

Land surface temperature is used as the target variable of the modeling workflow.

LST is extracted from the Landsat Collection 2 Level-2 thermal product and converted to physical temperature units according to the scale factors and offsets defined for the product.

All LST layers must be checked for:

- valid physical range;
- missing values;
- spatial artifacts;
- abnormal scene-level statistics;
- consistency across sensors and years.

## 6. Surface reflectance and spectral indices

Spectral indices are computed from surface reflectance bands after masking invalid pixels.

The expected spectral indices include:

- NDVI;
- NDWI;
- NDBI;
- UI;
- SAVI.

The final list of indices must match the manuscript and the model input configuration.

## 7. Cross-sensor consistency

Because the study uses a multi-decadal Landsat sequence, cross-sensor consistency is critical.

The workflow must account for differences among:

- Landsat 8 OLI/TIRS;
- Landsat 9 OLI-2/TIRS-2.

Potential issues include differences in spectral response, thermal bands, data gaps, and acquisition conditions.

## 8. Normalization

Normalization parameters must be estimated using training data only.

This is required to avoid information leakage between training, validation, and test datasets.

The recommended strategy is:

- robust min-max scaling for spectral indices;
- standardization of LST using training-period statistics.

All normalization parameters must be stored or documented to allow reproducible inference.

## 9. Output products

The preprocessing stage should produce spatially consistent raster layers for each retained acquisition date.

Expected outputs include:

- masked LST raster;
- masked spectral index rasters;
- valid-pixel mask;
- metadata table;
- quality-control summary.

Large preprocessing outputs are not stored in this GitHub repository.

## 10. Quality control

Each preprocessed scene should be evaluated using summary diagnostics, including:

- valid-pixel percentage;
- minimum, maximum, mean, and standard deviation of LST;
- spectral index ranges;
- visual inspection of masks;
- detection of anomalous scenes.

Scenes failing quality-control criteria should be excluded or explicitly flagged.

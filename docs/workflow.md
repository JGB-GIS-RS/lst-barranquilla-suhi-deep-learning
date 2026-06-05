# Methodological workflow

This document describes the computational workflow implemented for modeling surface urban heat patterns in Barranquilla, Colombia, using Landsat-derived land surface temperature, spectral indices, and deep learning models.

## 1. Landsat data inventory

The workflow begins with the identification and organization of Landsat Collection 2 Level-2 scenes covering the Barranquilla study area.

The inventory includes:

- satellite platform and sensor;
- acquisition date;
- path/row;
- cloud cover metadata;
- processing level;
- scene identifier;
- temporal grouping for modeling.

Only scenes meeting the methodological criteria of the study are retained for preprocessing and tensor construction.

## 2. Preprocessing

Preprocessing includes the preparation of Landsat-derived variables before model construction.

The main operations are:

- filtering by acquisition date and study area;
- masking clouds, cloud shadows, cirrus, snow, and invalid pixels using quality assessment bands;
- extracting land surface temperature from Level-2 products;
- computing spectral indices from surface reflectance bands;
- harmonizing spatial resolution and grid alignment;
- clipping all variables to the study area.

The preprocessing stage produces spatially aligned raster layers suitable for temporal stacking.

## 3. Derived variables

The primary target variable is land surface temperature.

The explanatory variables are spectral indices derived from Landsat surface reflectance bands. These indices are used as proxies of vegetation condition, built-up intensity, moisture, and urban surface characteristics.

The final variable set may include:

- LST;
- NDVI;
- NDWI;
- NDBI;
- UI;
- SAVI;
- additional indices if retained after methodological screening.

Only variables used consistently across the temporal sequence are included in the final tensor dataset.

## 4. Normalization

Normalization is applied to ensure numerical stability during model training.

The procedure distinguishes between:

- spectral indices, which may be scaled using robust min-max normalization;
- LST, which may be standardized using training-period statistics to preserve thermal comparability across years.

Normalization parameters must be estimated using only the training data to avoid information leakage.

## 5. Temporal tensor construction

Preprocessed raster layers are organized into multi-temporal tensors.

Each tensor represents a sequence of spatial observations with the following conceptual structure:

```text
time × rows × columns × variables

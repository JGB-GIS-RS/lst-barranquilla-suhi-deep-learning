# Preprocessing

This document describes the implemented preprocessing workflow used to generate
annual, spatially harmonized Landsat-derived predictors and target variables for
the Barranquilla LST/SUHI modeling framework.

The public preprocessing workflow covers Landsat scene selection, quality
screening, physical-unit conversion, annual compositing, spectral-index
generation, grid harmonization, terrestrial-domain restriction, train-only
normalization, and quality-control reporting.

## 1. Processing objective

The preprocessing stage produces annual raster variables that are geometrically
consistent, radiometrically valid, and suitable for T3 tensor construction and
deep-learning model training.

The two principal data groups are:

- annual land surface temperature (LST), used as the target variable;
- annual Landsat-derived spectral indices, used as explanatory variables.

The workflow is designed to preserve pixel-to-pixel correspondence across years
and to prevent information from validation or test periods from entering the
normalization stage.

## 2. Landsat products

The workflow uses Landsat 8 and Landsat 9 Collection 2 Level-2 Science Products:

```text
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

These products provide:

- atmospherically corrected surface reflectance;
- operational land surface temperature;
- `QA_PIXEL` quality flags;
- `QA_RADSAT` radiometric-saturation flags.

The analysis is restricted to Landsat 8/9 to maintain sensor-family consistency
throughout the 2013-2025 operational period.

## 3. Spatial and temporal scope

```text
Study domain: Barranquilla Metropolitan Area and surrounding urban-peri-urban domain
WRS-2 path/row: 009/052
Operational period: 2013-2025
Projected CRS: EPSG:32618
Nominal spatial resolution: 30 m
```

The year 2026 is excluded because it does not represent a complete annual period
comparable with the preceding years.

## 4. Physical-unit conversion

Surface-reflectance bands and the Landsat thermal product were transformed using
the official Collection 2 scale factors and offsets before spectral-index
calculation and annual compositing.

LST was converted to degrees Celsius before normalization.

The physical-unit conversion is implemented in:

```text
notebooks/02_preprocessing_lst_indices.ipynb
```

## 5. Quality assurance and valid-observation screening

The valid-observation domain combines atmospheric quality, radiometric validity,
and effective data availability.

The preprocessing workflow excludes observations flagged as:

- fill;
- dilated cloud;
- cloud;
- cloud shadow;
- snow or ice;
- radiometrically saturated.

Atmospheric screening is derived from `QA_PIXEL`, while radiometric saturation is
screened using `QA_RADSAT`.

Final validity also requires effective availability of the surface-reflectance
bands and LST after clipping, reprojection, resampling, and grid harmonization.
Therefore, a pixel is accepted only when the required predictor and target values
are physically available and not coded as NoData.

The exact masking logic is implemented and audited in:

```text
notebooks/02_preprocessing_lst_indices.ipynb
notebooks/03_quality_control.ipynb
```

## 6. Annual compositing

The workflow generates one annual product per variable for each year from 2013
through 2025.

For every variable, valid scene-level observations are aggregated using a
pixel-wise annual median.

A pixel is accepted in an annual composite only when at least two valid
observations are available for that year.

Annual valid-observation count layers are generated as diagnostics for:

- LST;
- the spectral-index group.

The annual composites are interpreted as comparable annual surface states. They
do not represent daily extremes, intra-seasonal thermal variability, or
short-duration atmospheric events.

## 7. Spectral indices

The final predictor set is fixed and consists of:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

The indices represent the following surface-response domains:

- `NDVI`: vegetation vigor and photosynthetically active cover;
- `NDMI`: vegetation/canopy moisture and water-stress response;
- `NDBI`: built-up and impervious-surface response;
- `UI`: urban spectral response with emphasis on SWIR2;
- `SAVI`: vegetation response adjusted for soil background;
- `BSI`: bare-soil and exposed-surface response.

The NIR-SWIR1 moisture index is reported as `NDMI`, not `NDWI`, to distinguish it
from the Green-NIR water index commonly used for open-water detection.

The six indices are retained as joint predictors. No univariate feature-selection
step is applied before deep-learning model training.

## 8. Spatial harmonization

All annual products are aligned to a common canonical raster grid.

The harmonized products share:

- coordinate reference system;
- spatial resolution;
- spatial extent;
- affine transform;
- pixel alignment;
- NoData convention.

Residual geometric discrepancies are corrected against the canonical reference
grid using controlled clipping, extent adjustment, reprojection, or resampling.

Continuous variables are resampled using bilinear interpolation. Discrete count
layers are handled using nearest-neighbor resampling.

The spatial harmonization ensures strict correspondence across variables and
years before T3 tensor construction.

## 9. Terrestrial modeling domain

The modeling domain is restricted to valid terrestrial surfaces.

Open-water and structurally non-land areas are excluded to avoid mixing distinct
land and water thermal regimes.

The final terrestrial domain combines:

- the annual valid-observation masks;
- the structural land-domain mask;
- the effective availability of spectral predictors;
- the effective availability of target-year LST.

Pixels outside this domain may remain geometrically present inside rectangular
patches but are excluded from optimization and evaluation through binary masks.

## 10. Train-only normalization

Normalization parameters are estimated exclusively from the training data and
then frozen for validation and test years.

### 10.1 LST target

LST is normalized using a global z-score:

```text
LST_z = (LST - mu_train) / sigma_train
```

The parameters are estimated only from valid target-year LST observations for:

```text
Training target years: 2016-2019
```

The same `mu_train` and `sigma_train` values are applied without recalibration to
validation and test targets.

### 10.2 Spectral predictors

Each spectral index is transformed to `[0, 1]` using robust train-only percentile
limits:

```text
p2.5 and p97.5
```

Because the training targets are 2016-2019 under the T3 formulation, the
antecedent predictor years used to estimate the spectral scaling parameters are:

```text
2013-2018
```

Values outside the retained percentile interval are clipped to the valid
normalized range.

### 10.3 Climate-radiative descriptors

The two descriptors used by M5 are normalized using parameters estimated only
from the training target years:

```text
2016-2019
```

No validation or test year contributes to the estimation of normalization
parameters.

The normalization workflow is implemented and audited in:

```text
notebooks/04_train_only_normalization.ipynb
notebooks/05_climate_forcing_integration.ipynb
```

## 11. Quality-control products

The preprocessing workflow generates compact audit products that document:

- scene and file availability;
- raster dimensions and geometry;
- CRS and transform consistency;
- valid-pixel counts and fractions;
- annual observation density;
- physical-value ranges;
- normalized-value ranges;
- missing or inconsistent products;
- mask and land-domain consistency.

Lightweight CSV, JSON, and PNG summaries are stored under:

```text
data/quality_control/
data/normalization/
docs/figures/quality_control/
docs/figures/normalization/
```

These files support methodological inspection but do not replace the
full-resolution rasters used in the computational workflow.

## 12. Main outputs

The preprocessing stage produces external annual raster products for:

```text
LST
NDVI
NDMI
NDBI
UI
SAVI
BSI
valid-observation counts
validity masks
```

It also produces the frozen train-only normalization parameters required for
tensor construction, model training, evaluation, and inverse transformation to
physical units.

The large annual GeoTIFFs, aligned raster stacks, and normalized full-resolution
products are intentionally excluded from GitHub.

## 13. Reproducibility boundary

The authoritative preprocessing implementations are:

```text
notebooks/01_scene_inventory_landsat_8_9.ipynb
notebooks/02_preprocessing_lst_indices.ipynb
notebooks/03_quality_control.ipynb
notebooks/04_train_only_normalization.ipynb
```

The public repository supports methodological inspection, traceability, and
partial reproducibility.

Complete regeneration requires:

- access to Landsat 8/9 Collection 2 Level-2 products;
- access to the study-domain geometry and canonical grid;
- adequate geospatial storage;
- a compatible Python/Google Earth Engine environment;
- sufficient computational resources for annual raster processing.

The notebooks and manuscript remain the authoritative descriptions of the
implemented preprocessing workflow.

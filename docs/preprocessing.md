# Preprocessing

This document describes the implemented preprocessing workflow used to generate annual, spatially harmonized Landsat-derived predictors and target variables for the Barranquilla LST/SUHI modeling framework.

The public preprocessing workflow covers Landsat scene selection, quality screening, physical-unit conversion, annual compositing, spectral-index generation, grid harmonization, terrestrial-domain restriction, fixed pre-validation normalization, and quality-control reporting.

## 1. Processing objective

The preprocessing stage produces annual raster variables that are geometrically consistent, radiometrically valid, and suitable for T3 tensor construction and deep-learning model training.

The two principal data groups are:

- annual land surface temperature (LST), used as the target variable;
- annual Landsat-derived spectral indices, used as explanatory variables.

The workflow is designed to preserve pixel-to-pixel correspondence across years and to prevent information from validation or test periods from entering the Landsat normalization stage.

## 2. Landsat products

The workflow uses Landsat 8 and Landsat 9 Collection 2 Level-2 Science Products:

```text
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

These products provide atmospherically corrected surface reflectance, operational land surface temperature, `QA_PIXEL` quality flags, and `QA_RADSAT` radiometric-saturation flags.

The analysis is restricted to Landsat 8/9 to maintain sensor-family consistency throughout the 2013-2025 operational period.

## 3. Spatial and temporal scope

```text
Study domain: Barranquilla Metropolitan Area and surrounding urban-peri-urban domain
WRS-2 path/row: 009/052
Operational period: 2013-2025
Projected CRS: EPSG:32618
Nominal spatial resolution: 30 m
```

The year 2026 is excluded because it does not represent a complete annual period comparable with the preceding years.

## 4. Physical-unit conversion

Surface-reflectance bands and the Landsat thermal product were transformed using the official Collection 2 scale factors and offsets before spectral-index calculation and annual compositing. LST was converted to degrees Celsius before normalization.

Implementation:

```text
notebooks/02_preprocessing_lst_indices.ipynb
```

## 5. Quality assurance and valid-observation screening

The valid-observation domain combines atmospheric quality, radiometric validity, and effective data availability.

The preprocessing workflow excludes observations flagged as fill, dilated cloud, cloud, cloud shadow, snow or ice, or radiometrically saturated. Atmospheric screening is derived from `QA_PIXEL`, while radiometric saturation is screened using `QA_RADSAT`.

Final validity also requires effective availability of the surface-reflectance bands and LST after clipping, reprojection, resampling, and grid harmonization. A pixel is accepted only when the required predictor and target values are physically available and not coded as NoData.

The exact masking logic is implemented and audited in:

```text
notebooks/02_preprocessing_lst_indices.ipynb
notebooks/03_quality_control.ipynb
```

## 6. Annual compositing

The workflow generates one annual product per variable for each year from 2013 through 2025. Valid scene-level observations are aggregated using a pixel-wise annual median.

A pixel is accepted in an annual composite only when at least two valid observations are available for that year. Annual valid-observation count layers are generated for LST and the spectral-index group.

The annual composites are interpreted as comparable annual surface states. They do not represent daily extremes, intra-seasonal thermal variability, or short-duration atmospheric events.

## 7. Spectral indices

The final predictor set is fixed and consists of:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

The indices represent vegetation vigor, vegetation/canopy moisture, built-up response, urban spectral response, vegetation response adjusted for soil background, and bare-soil/exposed-surface response, respectively.

The NIR-SWIR1 moisture index is reported as `NDMI`, not `NDWI`, to distinguish it from the Green-NIR water index commonly used for open-water detection.

The six indices are retained as joint predictors. No univariate feature-selection step is applied before deep-learning model training.

## 8. Spatial harmonization

All annual products are aligned to a common canonical raster grid and share coordinate reference system, spatial resolution, spatial extent, affine transform, pixel alignment, and NoData convention.

Residual geometric discrepancies are corrected against the canonical reference grid using controlled clipping, extent adjustment, reprojection, or resampling. Continuous variables are resampled using bilinear interpolation; discrete count layers use nearest-neighbor resampling.

## 9. Terrestrial modeling domain

The modeling domain is restricted to valid terrestrial surfaces. Open-water and structurally non-land areas are excluded to avoid mixing distinct land and water thermal regimes.

The final terrestrial domain combines annual valid-observation masks, the structural land-domain mask, effective availability of spectral predictors, and effective availability of target-year LST.

Pixels outside this domain may remain geometrically present inside rectangular patches but are excluded from optimization and evaluation through binary masks.

## 10. Landsat normalization for the reference experiment

The reported experiment used a fixed pre-validation reference period of **2013-2019** to estimate normalization parameters for both annual Landsat LST and the six spectral indices. These parameters were then frozen and applied unchanged to later years. VALIDATION (2020-2022) and TEST (2023-2025) did not contribute to their estimation.

The historical notebook and directory names retain the identifier `train_only`, but this label should not be interpreted as meaning that Landsat parameters were estimated only from the supervised target TRAIN years 2016-2019.

### 10.1 LST

LST is normalized using a global z-score:

```text
LST_z = (LST - mu_ref) / sigma_ref
```

with the reference-experiment parameters:

```text
Reference period: 2013-2019
mu_ref    = 38.48322677612305 °C
sigma_ref = 4.032179355621338 °C
n         = 4,301,427 valid reference values
```

The same parameters are applied without recalibration to subsequent years.

### 10.2 Spectral predictors

Each spectral index is transformed to `[0,1]` using robust percentile-based min-max scaling. For each index, the lower and upper limits are estimated from valid terrestrial values for 2013-2019 using:

```text
p2 and p98
```

Values outside the retained interval are clipped to `[0,1]`. The frozen index-specific values are stored in:

```text
data/normalization/04_normalization_parameters_train_only_2013_2025.csv
data/normalization/04_normalization_parameters_train_only_2013_2025.json
```

### 10.3 Climate-radiative descriptors

The two descriptors used by M5 follow a separate normalization path in Notebook 05. Their parameters are estimated from the target TRAIN years:

```text
2016-2019
```

and then maintained fixed for VALIDATION and TEST.

Implementation:

```text
notebooks/04_train_only_normalization.ipynb
notebooks/05_climate_forcing_integration.ipynb
```

## 11. Quality-control products

The preprocessing workflow generates compact audit products documenting scene and file availability, raster geometry, CRS and transform consistency, valid-pixel counts, annual observation density, physical-value ranges, normalized-value ranges, and mask/domain consistency.

Lightweight CSV, JSON, and PNG summaries are stored under:

```text
data/quality_control/
data/normalization/
docs/figures/quality_control/
docs/figures/normalization/
```

Historical normalization diagnostics generated during an earlier public reconstruction may reflect the former P2.5-P97.5 reconstruction. They are retained as historical QC artifacts and are not the authoritative parameter record for the reported experiment. The current Notebook 04 and the aligned parameter CSV/JSON define the reference-experiment normalization.

## 12. Main outputs

The preprocessing stage produces external annual raster products for LST, NDVI, NDMI, NDBI, UI, SAVI, BSI, valid-observation counts, and validity masks. It also produces the frozen Landsat normalization parameters required for tensor construction, model training, evaluation, and inverse transformation to physical units.

The large annual GeoTIFFs, aligned raster stacks, and normalized full-resolution products are intentionally excluded from GitHub.

## 13. Reproducibility boundary

The authoritative preprocessing implementations are:

```text
notebooks/01_scene_inventory_landsat_8_9.ipynb
notebooks/02_preprocessing_lst_indices.ipynb
notebooks/03_quality_control.ipynb
notebooks/04_train_only_normalization.ipynb
```

The public repository supports methodological inspection, traceability, and partial reproducibility. Complete regeneration requires access to Landsat 8/9 Collection 2 Level-2 products, the study-domain geometry and canonical grid, adequate geospatial storage, a compatible Python/Google Earth Engine environment, and sufficient computational resources.

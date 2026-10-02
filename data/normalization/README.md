# Normalization records

This directory contains lightweight parameter, audit, and summary products associated with the Landsat normalization stage implemented in:

```text
notebooks/04_train_only_normalization.ipynb
```

The historical notebook filename retains the term `train_only`, but the reference experiment used a **pre-validation Landsat reference period of 2013–2019** for both annual LST and the six spectral indices. Validation (2020–2022) and TEST (2023–2025) did not contribute to the estimation of these Landsat normalization parameters.

## Reference-experiment parameterization

### LST

Annual LST is standardized using a global z-score:

```text
LST_z = (LST - mu_ref) / sigma_ref
```

with parameters estimated from valid terrestrial LST values for 2013–2019:

```text
mu_ref    = 38.48322677612305 °C
sigma_ref = 4.032179355621338 °C
n         = 4,301,427
```

The same parameters are applied without recalibration to later years.

### Spectral predictors

The six indices are:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

For each index, robust min–max scaling uses the 2nd and 98th percentiles estimated from valid terrestrial values for 2013–2019:

```text
p2 and p98
```

Values outside the retained interval are clipped to `[0,1]`. The frozen reference values are stored in:

```text
04_normalization_parameters_train_only_2013_2025.csv
04_normalization_parameters_train_only_2013_2025.json
```

These files have been aligned to the parameter record used by the reference experiment.

### Climate-radiative descriptors

The two NASA POWER descriptors used by M5 are handled separately in Notebook 05. Their normalization parameters are estimated from the target TRAIN years 2016–2019 and then frozen for VALIDATION and TEST.

## Included files

### Geometry and input checks

- `04_input_v6_geometry_audit_2013_2025.csv`: raster dimensions, CRS, affine transform, extent, and input consistency.
- `04_normalized_products_file_existence_2013_2025.csv`: expected normalized products and file-availability checks.
- `04_train_spatial_mask_summary_2013_2025.csv`: summary of the spatial mask used during parameter estimation.

### Reference normalization parameters

- `04_normalization_parameters_train_only_2013_2025.csv`
- `04_normalization_parameters_train_only_2013_2025.json`
- `04_train_only_normalization_summary_2013_2025.csv`
- `04_train_only_normalization_summary_2013_2025.json`

The `2013_2025` suffix denotes the operational product period, not the years used to estimate parameters.

### Historical reconstruction diagnostics

`04_normalization_audit_values_2013_2025.csv` and normalization figures generated during an earlier public reconstruction may reflect the former P2.5–P97.5 reconstruction and are **not the authoritative parameter record for the reported experiment**. They are retained only as historical QC artifacts. The authoritative reference-experiment parameterization is the one stated above and implemented by the current Notebook 04.

## Naming note

The identifier `v6` in filenames and external paths is a historical processing label retained for traceability. It does not define a separate scientific model experiment.

Likewise, `train_only` in historical paths should be interpreted as a leakage-control label indicating that VALIDATION and TEST were excluded from parameter estimation; for Landsat normalization, the actual reference period was 2013–2019.

## Reproducibility boundary

The public repository does not store the full-resolution normalized GeoTIFFs, tensor archives, patch datasets, trained checkpoints, or full-resolution prediction products. Complete regeneration therefore requires the external research archive and compatible computational resources.

Related documentation:

```text
notebooks/04_train_only_normalization.ipynb
docs/preprocessing.md
docs/modeling.md
docs/reproducibility_notes.md
```

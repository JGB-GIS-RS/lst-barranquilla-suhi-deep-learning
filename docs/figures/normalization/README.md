# Normalization figures

This directory contains lightweight normalization diagnostics associated with the public preprocessing reconstruction.

## Reference-experiment normalization

The reported experiment uses the following Landsat normalization record:

```text
Reference period: 2013–2019
LST: global z-score
mu = 38.48322677612305 °C
sigma = 4.032179355621338 °C
Spectral indices: robust P2–P98 min-max, clipped to [0,1]
```

VALIDATION (2020–2022) and TEST (2023–2025) do not contribute to estimation of these Landsat parameters. The two NASA POWER descriptors used by M5 are normalized separately from target TRAIN years 2016–2019.

## Status of the figures in this directory

The existing PNG figures were produced during an earlier public reconstruction of Notebook 04 that used slightly different normalization settings. They are retained only as **historical quality-control graphics** and must not be used as the authoritative parameter record for the reported experiment.

The authoritative reference-experiment normalization is defined by:

```text
notebooks/04_train_only_normalization.ipynb
data/normalization/04_normalization_parameters_train_only_2013_2025.csv
data/normalization/04_normalization_parameters_train_only_2013_2025.json
notebooks/09_physical_unit_evaluation_and_exports_figures.ipynb
```

The historical notebook filename retains `train_only` for traceability. For Landsat variables, the actual reference period is 2013–2019.

## Reproducibility boundary

Full-resolution normalized GeoTIFFs are not stored in this repository. Regenerating updated diagnostic figures requires access to the external annual raster products and the canonical project mask/grid.

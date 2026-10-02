# Reproducibility notes

This document defines the reproducibility scope, computational requirements, and explicit limitations of the public Barranquilla LST/SUHI repository.

## 1. Reproducibility objective

The repository provides a transparent and traceable computational record for the implemented retrospective modeling workflow based on Landsat 8/9 Collection 2 Level-2 products, annual LST and spectral-index products, fixed pre-validation Landsat normalization, regional climate-radiative descriptors from NASA POWER, T3 tensor construction, deep-learning models M1-M5, statistical and spatial evaluation, EMC-BUILT-based non-urban reference construction, derived continuous SUHI diagnostics, and urban-to-peripheral thermal-gradient analysis.

Operational Landsat period:

```text
2013-2025
```

The year 2026 is excluded because it does not represent a complete annual period comparable with the preceding years.

## 2. Included public components

The final public release includes methodological documentation, the complete notebook sequence from `01` to `11`, configuration-reference files, dependency and environment files, lightweight CSV/JSON/YAML/PNG products, scene-inventory summaries, quality-control and normalization audits, NASA POWER annual and interannual descriptor summaries, model-training/comparison/evaluation notebooks, and final SUHI-reference and thermal-gradient notebooks.

The notebooks are the authoritative executable implementation. YAML files under `configs/` provide structured configuration references but are not automatically loaded by every notebook at runtime.

## 3. Intentionally excluded products

Large geospatial and machine-learning products are not stored in this repository. Excluded products include original Landsat scenes, full-resolution annual GeoTIFFs, aligned raster stacks and cubes, normalized full-resolution rasters, tensor archives, patch datasets, trained model weights/checkpoints, full-resolution prediction and residual maps, and temporary processing products.

These exclusions define the storage boundary of the public release.

## 4. Reproduction boundary

The repository supports methodological inspection and partial reproducibility. Complete end-to-end regeneration requires external access to the study-area geometry, canonical grid, large annual raster products, tensors/patches, model checkpoints or retraining resources, full-domain rasters, and adequate storage/GPU resources.

The public repository is not a one-click or fully self-contained reproduction package.

## 5. Temporal formulation and partitions

The retrospective modeling task uses:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

with the fixed target-year partition:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

The split is assigned by target year before patch extraction. This is a temporally controlled retrospective evaluation, not an independent spatial-block validation.

## 6. Normalization and leakage-control measures

The reported experiment uses two distinct normalization scopes.

### Landsat LST and spectral indices

For Landsat-derived annual LST and the six spectral indices, normalization parameters are estimated from the fixed **2013-2019 pre-validation reference period** and then frozen for all later years.

- LST: global z-score with `mu_ref = 38.48322677612305 °C` and `sigma_ref = 4.032179355621338 °C`.
- Spectral indices: robust percentile-based min-max scaling using `P2-P98`, clipped to `[0,1]`.
- VALIDATION years 2020-2022 and TEST years 2023-2025 do not contribute to estimation of these Landsat normalization parameters.

The historical notebook/path label `train_only` is retained for traceability and should be interpreted as a leakage-control label rather than as a literal statement that Landsat parameters were estimated only from target TRAIN years 2016-2019.

### M5 climate-radiative descriptors

The two NASA POWER descriptors are normalized separately using parameters estimated from the target TRAIN years 2016-2019 and then frozen for VALIDATION and TEST.

Additional controls are:

- target-year LST is never used as an input predictor;
- antecedent LST maps are not used as predictors;
- model predictions and residuals are not reused as model inputs;
- validation is used for learning-rate adjustment, checkpoint selection, and early stopping;
- TEST is reserved for final retrospective evaluation.

The M5 descriptors are associated with the target year. They do not contain target-year LST, but their inclusion makes M5 a retrospective estimate conditioned on externally known target-year covariates rather than an autonomous antecedent-only forecast.

## 7. Computational environment

The repository provides:

```text
requirements.txt
environment.yml
```

Several notebooks were developed for Google Colab and use external Google Drive paths. Users must adapt these paths to their environment. Full training may require CUDA-compatible GPU acceleration, sufficient RAM/GPU memory, substantial storage, Python geospatial libraries, PyTorch, Google Earth Engine access, and internet access for NASA POWER retrieval.

## 8. Reproducibility levels

### Level 1: Methodological inspection

Users can inspect documentation, notebooks, configuration references, scene inventories, audit tables, model definitions, training logic, metric calculations, and stored lightweight outputs.

### Level 2: Partial computational reproduction

Users with the required external intermediate inputs can rerun selected stages including normalization, tensor or patch validation, model training, model comparison, physical-unit metric conversion, and figure generation.

### Level 3: Complete regeneration

A complete reconstruction from original Landsat products through model training, full-domain spatial diagnostics, non-urban reference construction, and derived SUHI analysis requires the full external data chain, compatible software, storage, and sufficient computational resources.

## 9. Model-comparison interpretation

M1-M4 use the same spectral T3 predictor content and fixed temporal partitions. M5 adds two regional climate-radiative descriptors and uses a model-specific training protocol. Therefore, M4-M5 compares complete experimental configurations and must not be interpreted as a pure climate ablation, an isolated estimate of descriptor contribution, evidence of causal atmospheric control, or evidence of autonomous future forecasting skill.

## 10. Spatial evaluation limitations

Patch-based TEST metrics and reconstructed full-domain metrics are not numerically interchangeable because they differ in spatial support and reconstruction. Local zoom windows are illustrative spatial diagnostics, not independent validation subsets.

Spatial autocorrelation, shared geographic coverage across years, and smoothing of local thermal extremes must be considered when interpreting performance.

## 11. Prospective-component boundary

The current public release documents only the retrospective LST modeling workflow. Prospective land-cover simulation and future LST/SUHI projection components are outside this repository scope.

## 12. Expected reviewer use

A reviewer should be able to verify the Landsat operational period and scene inventory; inspect preprocessing, masking, normalization, T3 construction, model architectures, temporal partitions, evaluation logic, leakage controls, EMC-BUILT reference construction, continuous SUHI derivation, and the urban-to-peripheral thermal-gradient analysis.

The reference-experiment Landsat normalization parameters are preserved in:

```text
notebooks/04_train_only_normalization.ipynb
data/normalization/04_normalization_parameters_train_only_2013_2025.csv
data/normalization/04_normalization_parameters_train_only_2013_2025.json
notebooks/09_physical_unit_evaluation_and_exports_figures.ipynb
```

## 13. Final status

This repository is complete for its declared public retrospective scope through Notebook 11. Future changes should be limited to corrections, documentation improvements, publication metadata, or explicitly versioned extensions.

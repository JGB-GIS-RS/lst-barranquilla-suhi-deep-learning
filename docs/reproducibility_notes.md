# Reproducibility notes

This document defines the reproducibility scope, computational requirements, and
explicit limitations of the public Barranquilla LST/SUHI repository.

## 1. Reproducibility objective

The repository provides a transparent and traceable computational record for the
implemented retrospective modeling workflow based on:

- Landsat 8/9 Collection 2 Level-2 products;
- annual LST and spectral-index products;
- train-only normalization;
- regional climate-radiative descriptors derived from NASA POWER;
- T3 tensor construction;
- deep-learning models M1-M5;
- statistical and spatial evaluation.

The operational Landsat period is:

```text
2013-2025
```

The year 2026 is excluded because it does not represent a complete annual period
comparable with the preceding years.

The repository is designed to allow reviewers and researchers to inspect:

- data-source and scene-selection logic;
- preprocessing and quality-control procedures;
- normalization and leakage-control decisions;
- T3 temporal formulation;
- model architectures and training protocols;
- model-comparison logic;
- physical-unit evaluation;
- figure and spatial-diagnostic generation.

## 2. Included public components

The final public release includes:

- methodological documentation;
- the complete notebook sequence from `01` to `09`;
- configuration-reference files;
- dependency and environment files;
- lightweight CSV, JSON, YAML, and PNG products;
- scene-inventory summaries;
- quality-control and normalization audits;
- NASA POWER annual and interannual descriptor summaries;
- model-training, comparison, and evaluation notebooks.

The notebook sequence covers:

```text
01_scene_inventory_landsat_8_9.ipynb
02_preprocessing_lst_indices.ipynb
03_quality_control.ipynb
04_train_only_normalization.ipynb
05_climate_forcing_integration.ipynb
06_tensor_construction.ipynb
07_patch_extraction.ipynb
08A_train_M1_unet_baseline.ipynb
08B_train_M2_se_unet.ipynb
08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
08D_train_M4_convlstm_se_unet.ipynb
08E_train_M5_t3_climate_convlstm_se_unet.ipynb
08F_compare_models_M1_M5.ipynb
09_physical_unit_evaluation_and_exports_figures.ipynb
```

The notebooks are the authoritative executable implementation. The YAML files
under `configs/` provide structured configuration references but are not
automatically loaded by every notebook at runtime.

## 3. Intentionally excluded products

Large geospatial and machine-learning products are not stored in this repository.

The excluded products include:

- original Landsat scenes;
- full-resolution annual GeoTIFF products;
- aligned raster stacks and multi-year cubes;
- normalized full-resolution raster products;
- tensor archives;
- extracted patch datasets;
- trained model weights and checkpoints;
- full-resolution prediction and residual maps;
- temporary and intermediate processing outputs;
- large Google Drive artifacts used during execution.

These exclusions are deliberate and define the storage boundary of the public
release. They do not indicate that the repository is incomplete for its declared
scope.

## 4. Reproduction boundary

The repository supports methodological inspection and partial reproducibility.

Complete end-to-end regeneration requires external access to:

- Landsat 8/9 Collection 2 Level-2 products;
- the study-area geometry and canonical raster grid;
- the large annual raster products;
- tensor and patch archives;
- model checkpoints or sufficient resources for retraining;
- the full-domain rasters required for spatial mosaics and diagnostics;
- adequate storage and GPU-enabled computing resources.

The public repository does not constitute a one-click or fully self-contained
reproduction package.

Some products can be regenerated from the original data and notebooks, but the
repository does not guarantee that every large historical intermediate artifact
can be reproduced without access to the same external data environment and
processing dependencies used during the study.

## 5. Temporal formulation and partitions

The retrospective modeling task uses three antecedent annual spectral states:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

The fixed target-year partition is:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

The split is assigned by target year before patch extraction. Patches are not
randomly reassigned across the temporal partitions.

This is a temporally controlled retrospective evaluation. It is not an
independent spatial-block validation because the same geographic domain may
appear in different target years.

## 6. Leakage-control measures

The implemented workflow applies the following controls:

- LST normalization parameters are estimated only from training target years;
- spectral scaling parameters are estimated only from training antecedent
  predictors;
- climate-radiative descriptor scaling parameters are estimated only from
  training target years;
- normalization parameters remain fixed for validation and test;
- target-year LST is never used as an input predictor;
- antecedent LST maps are not used as predictors;
- model predictions and residuals are not reused as model inputs;
- validation is used for learning-rate adjustment, checkpoint selection, and
  early stopping;
- TEST is reserved for final retrospective evaluation.

The M5 descriptors are associated with the target year. They do not contain
target-year LST, but their inclusion changes the interpretation of M5: it is a
retrospective estimate conditioned on externally known target-year covariates,
not an autonomous antecedent-only forecast.

## 7. Computational environment

The repository provides:

```text
requirements.txt
environment.yml
```

These files document the expected Python and geospatial/deep-learning
dependencies.

Several notebooks were developed for Google Colab and use external Google Drive
paths. Users must adapt these paths to their own local or cloud environment.

Full training may require:

- CUDA-compatible GPU acceleration;
- sufficient RAM and GPU memory;
- substantial disk or cloud storage;
- Python geospatial libraries;
- PyTorch and supporting deep-learning libraries;
- Google Earth Engine access for the Landsat preprocessing stage;
- internet access for NASA POWER retrieval when rebuilding climate descriptors.

Exact runtime and hardware demand depend on the external raster, tensor, and
patch archives.

## 8. Reproducibility levels

### Level 1: Methodological inspection

Users can inspect:

- documentation;
- notebooks;
- configuration references;
- scene inventories;
- audit tables;
- model definitions;
- training logic;
- metric calculations;
- stored notebook outputs.

This level does not require the large external products.

### Level 2: Partial computational reproduction

Users with the required external intermediate inputs can rerun selected stages,
including:

- normalization audits;
- tensor or patch validation;
- model training;
- model comparison;
- physical-unit metric conversion;
- figure generation.

The exact executable subset depends on which external products are available.

### Level 3: Complete regeneration

A complete reconstruction from original Landsat products through model training
and full-domain spatial diagnostics requires the full external data chain,
adequate storage, compatible software, and sufficient computational resources.

The public repository documents this chain but does not contain all required
large-volume inputs and outputs.

## 9. Model-comparison interpretation

M1-M4 use the same spectral T3 predictor content and fixed temporal partitions,
allowing architectural comparison under broadly comparable conditions.

M5 adds two regional climate-radiative descriptors and uses a model-specific
training protocol. Therefore, the M4-M5 comparison represents two complete
experimental configurations.

It must not be interpreted as:

- a pure climate ablation;
- an isolated estimate of the contribution of the two NASA POWER variables;
- evidence of causal atmospheric control;
- evidence of autonomous future forecasting skill.

## 10. Spatial evaluation limitations

The repository documents both patch-based and full-domain evaluation.

These evaluation contexts are not numerically interchangeable:

- TEST patch metrics are computed from the external non-overlapping patch
  archive;
- full-domain metrics may be calculated from a spatial mosaic assembled through
  overlapping-window inference and weighting;
- local zoom windows are illustrative spatial diagnostics, not independent
  validation subsets.

Spatial autocorrelation, shared geographic coverage across years, and smoothing
of local thermal extremes must be considered when interpreting performance.

## 11. Prospective-component boundary

The current public release documents the retrospective LST modeling workflow.

It does not provide an executable public implementation of:

- CA-ANN/MOLUSCE future land-cover simulation;
- prospective spectral-predictor generation;
- conditioned 2035 LST/SUHI projection.

Those components correspond to a separate stage of the broader study and are
outside the declared reproducibility scope of this repository release.

## 12. Expected reviewer use

A reviewer should be able to use the repository to:

- verify the Landsat 8/9 operational period and WRS-2 scene inventory;
- inspect the annual-product and masking logic;
- verify train-only normalization procedures;
- inspect the T3 input construction;
- inspect the five model architectures;
- verify the temporal partition and evaluation logic;
- assess leakage-control decisions;
- reproduce selected lightweight outputs when the required external inputs are
  available;
- determine which claims are directly supported by the public computational
  record and which require external large-volume products.

## 13. Final status

This repository is complete for its declared public scope.

It provides the finalized retrospective notebook sequence, methodological
documentation, configuration references, lightweight audit products, and model
comparison workflow associated with the current manuscript.

No additional public notebooks are planned for this release.

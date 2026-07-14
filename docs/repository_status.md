# Repository status

This document summarizes the final public status of the
`lst-barranquilla-suhi-deep-learning` repository and defines its reproducibility
boundary.

## 1. Final public status

This repository is the public computational companion to the Barranquilla
LST/SUHI study.

It documents the implemented retrospective workflow for:

- Landsat 8/9 scene inventory;
- annual LST and spectral-index preprocessing;
- quality control;
- train-only normalization;
- NASA POWER regional climate-radiative descriptor construction;
- T3 tensor construction;
- spatial patch extraction;
- training of models M1-M5;
- model comparison;
- physical-unit evaluation;
- figure and spatial-diagnostic export.

The public release covers the 2013-2025 operational Landsat period. The year 2026
is excluded because it does not represent a complete annual period comparable
with the preceding years.

This repository should be interpreted as a finalized public research-code and
documentation archive for methodological inspection, traceability, and partial
reproducibility. No additional notebooks are required for the public release
associated with the current manuscript.

## 2. Included repository components

The repository includes:

- root-level project documentation;
- license and citation metadata;
- environment and dependency files;
- structured configuration references;
- lightweight data and audit products;
- methodological documentation;
- the complete public notebook sequence;
- a minimal `src/` directory retained for repository structure.

The principal top-level files and directories are:

```text
README.md
LICENSE
CITATION.cff
.gitignore
requirements.txt
environment.yml
configs/
data/
docs/
notebooks/
src/
```

## 3. Included notebooks

The complete public notebook sequence is:

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

These notebooks document the implemented public workflow from scene inventory to
model evaluation and figure generation.

## 4. Included documentation

The `docs/` directory contains:

```text
README.md
data_sources.md
preprocessing.md
modeling.md
workflow.md
reproducibility_notes.md
repository_status.md
figures/
```

These files describe the study data, preprocessing decisions, T3 formulation,
M1-M5 model design, leakage controls, evaluation logic, reproducibility boundary,
and final repository scope.

## 5. Included configuration references

The `configs/` directory contains:

```text
README.md
paths_example.yml
model_config.yml
training_config.yml
experiment_metadata.yml
```

These YAML files provide structured records of paths, architecture settings,
training parameters, temporal partitions, and experiment metadata.

The notebooks remain the authoritative executable implementations. The YAML files
are reference records and are not automatically loaded by all notebooks at
runtime.

## 6. Included lightweight data products

The `data/` directory contains compact products that support auditability and
methodological inspection:

```text
data/
├── scene_inventory/
├── quality_control/
├── normalization/
└── climate_forcings/
```

Depending on the subdirectory, included products may contain:

- scene-level and annual inventory tables;
- geometry and file-availability audits;
- valid-pixel summaries;
- normalization parameters;
- normalized-value diagnostics;
- NASA POWER annual summaries;
- interannual climate-radiative descriptors;
- exploratory climate-error diagnostics;
- compact JSON, CSV, and PNG records.

These products do not replace the large rasters, tensors, patch archives, or
model checkpoints used during the full computational workflow.

## 7. Modeling status

The implemented retrospective formulation is:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

The fixed target-year partition is:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

The evaluated model family is:

```text
M1: U-Net baseline
M2: SE U-Net
M3: ConvLSTM U-Net
M4: ConvLSTM-SE U-Net
M5: T3-Climate ConvLSTM-SE U-Net
```

M1-M4 form the controlled spectral-model comparison. M5 is a complete augmented
configuration that adds two target-year regional climate-radiative descriptors
and uses a model-specific training protocol.

Therefore, the M4-M5 contrast is not interpreted as a pure ablation that isolates
the effect of climate augmentation.

## 8. Data intentionally excluded

The following large-volume products are intentionally excluded from GitHub:

- original Landsat scenes;
- full-resolution annual GeoTIFF products;
- aligned raster stacks and multi-year cubes;
- tensor datasets;
- patch datasets;
- trained model weights and checkpoints;
- full-resolution prediction and residual maps;
- temporary preprocessing outputs;
- intermediate Google Drive products.

These exclusions are deliberate and define the storage boundary of the public
repository. They do not indicate that the public archive is unfinished.

## 9. Reproducibility status

The repository supports:

- inspection of scene-selection logic;
- review of preprocessing and quality-control decisions;
- verification of train-only normalization logic;
- inspection of T3 tensor and patch construction;
- inspection of the M1-M5 architectures and training procedures;
- review of leakage-control criteria;
- reproduction of lightweight tables and figures when the required external
  inputs are available;
- review of normalized and physical-unit evaluation logic.

The repository does not provide a fully self-contained end-to-end execution
package because the large computational inputs and outputs remain external.

Complete regeneration requires:

- access to Landsat 8/9 Collection 2 Level-2 products;
- access to the study-domain geometry and canonical grid;
- access to the external annual raster products;
- access to tensor and patch archives;
- access to model checkpoints or sufficient GPU resources for retraining;
- adequate local or cloud storage;
- a compatible Python, Google Earth Engine, and Google Colab environment.

## 10. Prospective component boundary

The current public repository documents the retrospective LST modeling workflow
and the computational analyses already completed for the manuscript.

It does not include an executable public implementation of:

- CA-ANN/MOLUSCE future land-cover simulation;
- future spectral-predictor generation;
- conditioned 2035 LST/SUHI projection.

These prospective components belong to a separate stage of the broader study and
are not part of the finalized public code archive represented here.

## 11. Reviewer interpretation

Reviewers should interpret this repository as:

- a final public computational companion;
- a transparent record of the implemented retrospective workflow;
- a source for inspecting model logic, preprocessing decisions, and evaluation
  procedures;
- a partial-reproducibility archive constrained by the deliberate exclusion of
  large-volume research data and model artifacts.

It should not be interpreted as:

- a fully self-contained data repository;
- a one-click reproduction package;
- a public archive of all intermediate rasters and checkpoints;
- a completed implementation of the future 2035 projection stage.

## 12. Final status statement

The repository is complete for its declared public scope.

It contains the finalized notebook sequence, supporting documentation,
configuration references, lightweight audit products, and retrospective model
comparison workflow required to accompany the current manuscript.

No further public notebooks are planned for this release.

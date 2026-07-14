# Documentation

This directory contains the methodological and reproducibility documentation
supporting the Barranquilla LST/SUHI study.

The documents describe the public computational workflow implemented with
Landsat 8/9 Collection 2 Level-2 products, annual spectral indices, regional
climate-radiative descriptors derived from NASA POWER, and spatial-temporal deep
learning models.

## Recommended reading order

Reviewers and users are encouraged to consult the documentation in the following
order:

1. `workflow.md`
2. `data_sources.md`
3. `preprocessing.md`
4. `modeling.md`
5. `reproducibility_notes.md`
6. `repository_status.md`

## Document descriptions

- `workflow.md`: summarizes the complete public workflow, from Landsat 8/9 scene
  inventory and annual product generation to tensor construction, patch
  extraction, model training, model comparison, and physical-unit evaluation.

- `data_sources.md`: documents the satellite and NASA POWER data sources,
  temporal coverage, scene-selection logic, spatial reference, and lightweight
  inventory products.

- `preprocessing.md`: describes quality masking, physical-unit conversion,
  annual compositing, spectral-index calculation, spatial harmonization,
  land-domain restriction, train-only normalization, and associated quality
  controls.

- `modeling.md`: documents the T3 regression formulation, the fixed temporal
  partition, the M1-M5 comparison design, U-Net, ConvLSTM, squeeze-and-excitation
  modules, regional climate-radiative descriptors, loss functions, evaluation
  metrics, and inferential limitations.

- `reproducibility_notes.md`: explains the scope of methodological inspection and
  partial reproducibility, the external computational requirements, excluded
  large files, leakage-control criteria, and the intended use of the repository
  by reviewers and researchers.

- `repository_status.md`: summarizes the components included in the final public
  release and clarifies how the repository should be interpreted in relation to
  the manuscript and the external large-volume data products.

## Documentation scope

The documentation covers the complete public notebook sequence currently included
in the repository:

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

The repository also includes lightweight scene-inventory, quality-control,
normalization, and NASA POWER summary products, together with configuration
references and documentation files.

This documentation represents the final public scope of the repository. No
additional notebooks are required for the public release associated with the
current manuscript.

## Terminology

The folder name `climate_forcings` and related historical file identifiers are
retained for consistency with the implemented workflow. Scientifically, the NASA
POWER variables used by M5 are interpreted as annual regional
climate-radiative descriptors associated with the target year, not as
spatially distributed climate fields at Landsat resolution.

The T3 formulation uses three antecedent annual states:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

The target-year partition is:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

## Data policy

Large geospatial and machine-learning products are intentionally excluded from
the GitHub repository, including:

- original Landsat scenes;
- full-resolution annual GeoTIFF products;
- raster stacks and multi-year cubes;
- tensor and patch datasets;
- trained model checkpoints;
- full-resolution prediction and residual maps;
- temporary preprocessing products.

The repository may include compact CSV, JSON, YAML, and PNG products when they
support methodological transparency, auditability, and interpretation.

Complete regeneration of the analysis requires external access to the original
data and large intermediate products, sufficient storage, and suitable
computational resources. The notebooks and manuscript remain the authoritative
descriptions of the implemented scientific workflow.

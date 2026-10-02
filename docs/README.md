# Documentation

This directory contains the methodological and reproducibility documentation supporting the Barranquilla LST/SUHI study.

The documents describe the public computational workflow implemented with Landsat 8/9 Collection 2 Level-2 products, annual spectral indices, regional climate-radiative descriptors derived from NASA POWER, and spatiotemporal deep-learning models.

## Recommended reading order

1. `workflow.md`
2. `data_sources.md`
3. `preprocessing.md`
4. `modeling.md`
5. `reproducibility_notes.md`
6. `repository_status.md`

## Document descriptions

- `workflow.md`: complete public workflow from Landsat scene inventory and annual products to model evaluation and derived SUHI diagnostics.
- `data_sources.md`: satellite and NASA POWER sources, temporal coverage, scene-selection logic, spatial reference, and lightweight inventory products.
- `preprocessing.md`: quality masking, physical-unit conversion, annual compositing, spectral indices, spatial harmonization, terrestrial-domain restriction, and fixed pre-validation Landsat normalization.
- `modeling.md`: T3 regression formulation, fixed temporal partition, M1–M5 comparison design, architectures, descriptors, loss functions, evaluation metrics, and inferential limitations.
- `reproducibility_notes.md`: scope of methodological inspection and partial reproducibility, external requirements, excluded large files, leakage-control criteria, and intended reviewer use.
- `repository_status.md`: final public scope and relation to the manuscript and external large-volume products.

## Documentation scope

The documentation covers the complete public notebook sequence:

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
10_emc_built_2022_reference_and_suhi_diagnostics.ipynb
11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb
```

The repository also includes lightweight scene-inventory, quality-control, normalization, and NASA POWER summary products, together with configuration references.

## Terminology

The historical notebook name `04_train_only_normalization.ipynb` is retained for traceability. For the reported experiment, Landsat LST and spectral-index parameters are estimated from the fixed pre-validation reference period **2013–2019** and then frozen for later years. VALIDATION and TEST do not contribute to these estimates.

The two NASA POWER descriptors used by M5 are normalized separately using target TRAIN years **2016–2019**.

The folder name `climate_forcings` is also retained for consistency with the implemented workflow. Scientifically, the NASA POWER variables used by M5 are annual regional climate-radiative descriptors associated with the target year, not spatially distributed climate fields at Landsat resolution.

T3 formulation:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

Target-year partition:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

## Data policy

Large geospatial and machine-learning products are intentionally excluded, including original Landsat scenes, full-resolution annual GeoTIFFs, raster stacks/cubes, tensor and patch datasets, trained checkpoints, full-resolution prediction/residual maps, and temporary products.

Compact CSV, JSON, YAML, and PNG products may be included when they support methodological transparency and auditability.

Complete regeneration requires external access to the original data and large intermediate products, sufficient storage, and suitable computational resources. The notebooks and manuscript remain the authoritative descriptions of the implemented scientific workflow.

# Notebooks

This directory contains the **complete public notebook collection** accompanying the study on annual urban land surface temperature (LST) prediction and conditioned SUHI projection for the Barranquilla Metropolitan Area, Colombia.

The repository is designed for **methodological inspection, code transparency, and partial reproducibility**. It is not intended to distribute the full computational archive. Large raster products, tensors, patch datasets, model checkpoints, and full-resolution prediction maps remain outside GitHub because of their volume and storage requirements.

## Public workflow

The notebooks document the processing and modeling sequence used in the retrospective component of the study.

| Order | Notebook | Main purpose |
|---:|---|---|
| 01 | `01_scene_inventory_landsat_8_9.ipynb` | Builds and audits the Landsat 8/9 Collection 2 Level-2 scene inventory for 2013–2025, WRS Path 9 / Row 52. |
| 02 | `02_preprocessing_lst_indices.ipynb` | Applies quality masking, derives annual LST and six spectral indices, and exports annual median composites. |
| 03 | `03_quality_control.ipynb` | Audits annual products, spatial alignment, valid-pixel coverage, value ranges, masks, and lightweight quality-control outputs. |
| 04 | `04_train_only_normalization.ipynb` | Estimates normalization parameters from the training domain only and applies fixed transformations to LST and spectral predictors. |
| 05 | `05_climate_forcing_integration.ipynb` | Builds annual NASA POWER descriptors and derives the two retained interannual climate–radiative covariates. |
| 06 | `06_tensor_construction.ipynb` | Constructs full-domain T3 tensors for the climate-augmented final model using 18 spectral channels and 2 regional climate–radiative channels. |
| 07 | `07_patch_extraction.ipynb` | Extracts train, validation, and test patches from the climate-augmented T3 tensors and generates spatial sampling diagnostics. |
| 08A | `08A_train_M1_unet_baseline.ipynb` | Trains M1, the 2D U-Net spatial baseline. |
| 08B | `08B_train_M2_se_unet.ipynb` | Trains M2, the SE U-Net model. |
| 08C | `08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb` | Trains M3, the ConvLSTM U-Net model with an explicit three-step spectral sequence. |
| 08D | `08D_train_M4_convlstm_se_unet.ipynb` | Trains M4, the ConvLSTM-SE U-Net model. |
| 08E | `08E_train_M5_t3_climate_convlstm_se_unet.ipynb` | Trains M5, the final T3-Climate ConvLSTM-SE U-Net with two regional climate–radiative descriptors. |
| 08F | `08F_compare_models_M1_M5.ipynb` | Compares M1–M5 under the fixed retrospective protocol and exports model rankings and summary figures. |
| 09 | `09_physical_unit_evaluation_and_exports_figures.ipynb` | Converts evaluation metrics to degrees Celsius and exports the physical-unit tables, figures, and optional raster-based diagnostics used for reporting. |

## Model sequence

The five evaluated configurations are:

| Model | Architecture | Input formulation |
|---|---|---|
| M1 | U-Net | 18-channel T3 spectral stack |
| M2 | SE U-Net | 18-channel T3 spectral stack |
| M3 | ConvLSTM U-Net | Three antecedent states × six spectral indices |
| M4 | ConvLSTM-SE U-Net | Three antecedent states × six spectral indices |
| M5 | T3-Climate ConvLSTM-SE U-Net | T3 spectral sequence plus two annual regional climate–radiative descriptors |

M1–M4 use the same spectral predictor content. M5 is a **climate-augmented final configuration**, not a pure architectural ablation of M4. The historical identifier `M5B` may remain in some internal paths or output names for traceability, whereas the public manuscript and repository designation is **M5**.

## Temporal protocol

The retrospective experiments use the fixed target-year partition:

```text
TRAIN      = 2016, 2017, 2018, 2019
VALIDATION = 2020, 2021, 2022
TEST       = 2023, 2024, 2025
```

Each target year is represented through a T3 sequence of the three antecedent annual spectral states. The year 2026 is excluded because it does not represent a complete annual period.

## Data and storage policy

The following products are intentionally **not stored in GitHub**:

- Landsat source imagery;
- annual GeoTIFF composites;
- harmonized and normalized raster stacks;
- full-domain tensor files;
- spectral-only and climate-augmented patch datasets;
- trained model checkpoints;
- full-resolution prediction and residual rasters.

These products are generated or consumed by the notebooks but remain in external storage. Lightweight inventories, CSV/JSON summaries, diagnostic figures, and notebook outputs may be retained when they improve methodological transparency.

## Execution requirements

The notebooks were developed primarily for Google Colab and Google Drive workflows.

- Google Earth Engine authentication is required for Landsat preprocessing.
- Internet access is required to obtain NASA POWER data when cached inputs are unavailable.
- A CUDA-capable GPU is recommended for notebooks 08A–08E.
- Local or cloud paths must be adapted to the user's environment.
- The training notebooks accept an external patch root through the `PATCH_ROOT` environment variable when the default Google Drive structure is not used.

Example:

```python
import os
os.environ["PATCH_ROOT"] = "/path/to/external/patches"
```

## Reproducibility scope

The repository supports:

- inspection of the Landsat preprocessing and quality-control logic;
- verification of train-only normalization procedures;
- inspection of the T3 tensor and patch formulations;
- review of the five model architectures and training procedures;
- reproduction of model comparison and physical-unit evaluation when the required external inputs and outputs are available.

The repository does **not** constitute a self-contained data package. Full end-to-end execution requires the external raster, tensor, patch, and checkpoint products described above, together with sufficient storage and computational resources.

## Repository status

The notebook sequence listed in this document is the **final public scope of the repository**. It covers scene inventory, preprocessing, quality control, normalization, climate integration, tensor and patch preparation, training of M1–M5, comparative evaluation, and physical-unit figure export. No additional notebooks are required for the public computational record of the manuscript.

# Notebooks

This directory contains the computational notebooks used to document and reproduce the main methodological workflow of the study.

The notebooks are organized sequentially according to the processing chain described in:

```
notebooks/notebook_index.md
```

## Purpose

The notebooks are intended to document and execute the main stages of the workflow:

1. Landsat 8/9 scene inventory.
2. Preprocessing, masking, LST extraction, and spectral index computation.
3. Quality control of scenes and derived variables.
4. Multi-temporal dataset and tensor construction.
5. Spatial-temporal patch extraction.
6. Baseline model training and evaluation.
7. U-Net, ConvLSTM, and SE-based model training.
8. Climate forcing integration, if retained in the final configuration.
9. Model evaluation and comparison.
10. Spatial residual diagnostics.
11. Prediction map and hotspot generation.

## Current notebook

The first notebook currently included in the repository is:

```
01_scene_inventory_landsat_8_9.ipynb
```

This notebook documents the Landsat 8/9 Collection 2 Level-2 scene inventory for the 2013–2025 operational period.

The associated lightweight inventory tables are stored in:

```
data/scene_inventory/
```

## Execution order

The expected execution order is defined in:

```
notebooks/notebook_index.md
```

The final notebook names must remain consistent with the manuscript, configuration files, and documentation.

## Data policy

Large input datasets are not stored in this repository.

This includes:

* Landsat scenes;
* raster stacks;
* tensors;
* patch datasets;
* model checkpoints;
* full-resolution prediction maps.

Lightweight tabular products, such as scene inventories and summary tables, may be included when they improve transparency and reproducibility.

Data access and reconstruction instructions are documented in:

```
data/README.md
docs/data_sources.md
docs/preprocessing.md
```

## Path configuration

Notebooks should avoid hard-coded personal paths.

Users should copy:

```
configs/paths_example.yml
```

as:

```
configs/paths.yml
```

and edit it according to their local or cloud environment.

The file `paths.yml` should not be committed if it contains personal or machine-specific paths.

## Reproducibility scope

At this stage, the notebooks support methodological inspection and partial reproducibility.

Full workflow reproduction will require access to external raster datasets, adequate storage, and sufficient computational resources.

## Current status

This directory currently includes the first executable notebook of the workflow:

```
01_scene_inventory_landsat_8_9.ipynb
```

Additional notebooks will be added as the preprocessing, tensor construction, model training, evaluation, and spatial diagnostics workflows are consolidated.

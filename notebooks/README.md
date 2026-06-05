# Notebooks

This directory contains the computational notebooks used to reproduce the main methodological workflow of the study.

The notebooks are organized sequentially according to the processing chain described in:

```text
notebook_index.md
```

## Purpose

The notebooks are intended to document and execute the main stages of the workflow:

1. Landsat scene inventory.
2. Preprocessing, masking, LST extraction, and spectral index computation.
3. Quality control of scenes and derived variables.
4. Multi-temporal tensor construction.
5. Spatial-temporal patch extraction.
6. Baseline model training and evaluation.
7. U-Net + ConvLSTM + channel attention model training.
8. Model evaluation and comparison.
9. Spatial residual diagnostics.
10. Prediction map and hotspot generation.

## Execution order

The expected execution order is defined in:

```text
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

Data access and reconstruction instructions are documented in:

```text
data/README.md
docs/data_sources.md
docs/preprocessing.md
```

## Path configuration

Notebooks should avoid hard-coded personal paths.

Users should copy:

```text
configs/paths_example.yml
```

as:

```text
configs/paths.yml
```

and edit it according to their local or cloud environment.

The file `paths.yml` should not be committed if it contains personal or machine-specific paths.

## Current status

This directory currently defines the planned notebook structure. Final executable notebooks will be added after the computational workflow is consolidated.


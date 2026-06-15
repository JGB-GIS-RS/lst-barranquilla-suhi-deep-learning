# Documentation

This directory contains methodological documentation supporting the transparency and reproducibility of the study.

The documentation describes the computational workflow for modeling land surface temperature and surface urban heat patterns in Barranquilla, Colombia, using Landsat 8/9 Collection 2 Level-2 products, spectral indices, interannual climate forcings, and deep learning models.

## Recommended reading order

Reviewers and users should read the documentation in the following order:

1. `workflow.md`
2. `data_sources.md`
3. `preprocessing.md`
4. `modeling.md`
5. `reproducibility_notes.md`
6. `repository_status.md`

## Document descriptions

* `workflow.md`: describes the complete methodological workflow, from Landsat 8/9 scene inventory to preprocessing, temporal dataset construction, model development, evaluation, and prediction outputs.
* `data_sources.md`: documents the satellite data sources, temporal coverage, scene selection logic, and lightweight inventory products.
* `preprocessing.md`: describes quality masking, LST extraction, spectral index computation, spatial harmonization, normalization, and quality control.
* `modeling.md`: documents the spatial-temporal regression formulation, T1/T2/T3 temporal sequence, model comparison strategy, U-Net, ConvLSTM, SE attention, climate forcings, metrics, and limitations.
* `reproducibility_notes.md`: explains the reproducibility scope, computational requirements, excluded large files, leakage control, and expected reviewer use.
* `repository_status.md`: summarizes which repository components are currently included, which components are pending, and how the repository should be interpreted at the current development stage.

## Current documentation scope

The current documentation supports methodological inspection and review of the initial reproducibility framework.

At this stage, the repository includes:

* Landsat 8/9 scene inventory documentation;
* lightweight scene inventory tables;
* preprocessing documentation;
* modeling documentation;
* configuration files;
* reproducibility notes;
* repository status documentation.

The repository is still under active development. Final preprocessing notebooks, tensor construction scripts, model training notebooks, evaluation notebooks, and spatial diagnostics notebooks will be added as the manuscript workflow is consolidated.

## Data policy

Large geospatial datasets are not stored in this repository.

The repository may include lightweight tabular products, such as Landsat scene inventories and summary tables, when they improve transparency and reproducibility.

Large raster datasets, tensors, patch datasets, model checkpoints, and prediction maps must remain outside the GitHub repository.

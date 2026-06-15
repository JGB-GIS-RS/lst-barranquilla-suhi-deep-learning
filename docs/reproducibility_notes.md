# Reproducibility notes

This document describes the reproducibility scope, computational requirements, and limitations of the repository.

## 1. Reproducibility objective

The objective of this repository is to provide a transparent and traceable computational workflow for modeling land surface temperature and surface urban heat patterns in Barranquilla, Colombia.

The workflow is based on Landsat 8/9 Collection 2 Level-2 products for the 2013–2025 operational period.

The repository is intended to allow users and reviewers to inspect:

* the methodological workflow;
* the Landsat 8/9 scene inventory;
* data reconstruction logic;
* preprocessing procedures;
* model configuration;
* training and evaluation strategy;
* expected outputs.

## 2. What is included

This repository includes or will include:

* methodological documentation;
* executable notebooks;
* reusable source code;
* configuration files;
* environment specifications;
* lightweight tabular products;
* lightweight examples, if needed.

The repository already includes the Landsat 8/9 scene inventory and summary tables for the 2013–2025 operational period.

The repository is structured to make the computational logic explicit and auditable.

## 3. What is not included

Large files are not stored in this GitHub repository.

The following files are excluded:

* original Landsat scenes;
* full-resolution GeoTIFF files;
* raster stacks;
* temporal tensors;
* extracted patch datasets;
* trained model weights;
* model checkpoints;
* large prediction maps;
* temporary processing outputs.

These files must be reconstructed from the documented workflow or stored externally using an appropriate storage system.

## 4. Data reconstruction

The input dataset must be reconstructed from Landsat 8/9 Collection 2 Level-2 products.

The operational period is:

```
2013–2025
```

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

The reconstruction process requires:

* defining the final study area;
* using the documented Landsat 8/9 scene inventory;
* applying quality masking;
* extracting LST;
* computing spectral indices;
* harmonizing spatial grids;
* constructing temporal datasets;
* extracting training, validation, and test patches.

The exact reconstruction process must be documented in the notebooks and configuration files.

## 5. Computational requirements

Full reproduction of the model training workflow may require:

* high-memory computing environment;
* GPU acceleration;
* sufficient disk storage for raster stacks and tensors;
* Python geospatial libraries;
* deep learning framework support;
* access to external geospatial datasets or cloud storage.

The workflow may be adapted to local workstations, Google Colab, or cloud-based environments.

## 6. Environment

Two environment files are provided:

* `requirements.txt` for pip-based installation;
* `environment.yml` for Conda-based installation.

These files define the expected computational dependencies.

Version-pinned environments should be generated after the final workflow has been successfully executed and validated.

## 7. Reproducibility levels

The repository supports different levels of reproducibility.

### Level 1: Methodological inspection

Users can inspect documentation, notebooks, configuration files, source-code structure, and lightweight tabular products without downloading large datasets.

### Level 2: Lightweight execution

Users can execute selected notebooks using lightweight tabular products or small sample data, if included.

The current scene inventory notebook can be inspected directly in GitHub and may be executed when the corresponding input inventory or Earth Engine access is available.

### Level 3: Full workflow reproduction

Users can reconstruct the full dataset and execute the complete preprocessing, training, evaluation, and mapping workflow.

Level 3 reproduction requires access to the complete input dataset and adequate computational resources.

## 8. Validation and leakage control

Spatial-temporal modeling is vulnerable to inflated performance if data partitioning is poorly designed.

The final workflow must explicitly document:

* training data;
* validation data;
* test data;
* temporal holdout logic;
* spatial holdout logic, if used;
* normalization parameters estimated from training data only.

Random patch-level splitting should be avoided unless its limitations are explicitly acknowledged and controlled.

## 9. Expected reviewer use

A reviewer should be able to use this repository to:

* understand the complete computational workflow;
* inspect the Landsat 8/9 scene inventory;
* verify the 2013–2025 operational period;
* confirm that 2026 was excluded because the annual period is incomplete;
* inspect the model architecture and preprocessing logic;
* verify that data leakage controls were considered;
* reproduce selected outputs when data and computational resources are available;
* evaluate whether the reported results are methodologically traceable.

## 10. Current status

This repository is under active development.

At this stage, the repository contains:

* the main repository documentation;
* data source documentation;
* preprocessing documentation;
* reproducibility notes;
* configuration files;
* environment files;
* source-code module structure;
* notebook index;
* Landsat 8/9 scene inventory notebook;
* Landsat 8/9 scene inventory tables for the 2013–2025 operational period.

Final preprocessing notebooks, modeling notebooks, source-code modules, and output-generation scripts will be added as the manuscript workflow is consolidated.

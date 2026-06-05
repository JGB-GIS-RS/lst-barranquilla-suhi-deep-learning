# Reproducibility notes

This document describes the reproducibility scope, computational requirements, and limitations of the repository.

## 1. Reproducibility objective

The objective of this repository is to provide a transparent and traceable computational workflow for modeling land surface temperature and surface urban heat patterns in Barranquilla, Colombia.

The repository is intended to allow users and reviewers to inspect:

- the methodological workflow;
- data reconstruction logic;
- preprocessing procedures;
- model configuration;
- training and evaluation strategy;
- expected outputs.

## 2. What is included

This repository includes or will include:

- methodological documentation;
- executable notebooks;
- reusable source code;
- configuration files;
- environment specifications;
- lightweight examples when possible.

The repository is structured to make the computational logic explicit and auditable.

## 3. What is not included

Large files are not stored in this GitHub repository.

The following files are excluded:

- original Landsat scenes;
- full-resolution GeoTIFF files;
- raster stacks;
- temporal tensors;
- extracted patch datasets;
- trained model weights;
- large prediction maps;
- temporary processing outputs.

These files must be reconstructed from the documented workflow or stored externally.

## 4. Data reconstruction

The input dataset must be reconstructed from Landsat Collection 2 Level-2 products.

The reconstruction process requires:

- defining the final study area;
- selecting the final image inventory;
- applying quality masking;
- extracting LST;
- computing spectral indices;
- harmonizing spatial grids;
- constructing temporal tensors;
- extracting training and validation patches.

The exact reconstruction process must be documented in the notebooks and configuration files.

## 5. Computational requirements

Full reproduction of the model training workflow may require:

- high-memory computing environment;
- GPU acceleration;
- sufficient disk storage for raster stacks and tensors;
- Python geospatial libraries;
- deep learning framework support.

The workflow may be adapted to local workstations, Google Colab, or cloud-based environments.

## 6. Environment

Two environment files are provided:

- `requirements.txt` for pip-based installation;
- `environment.yml` for Conda-based installation.

These files define the expected computational dependencies. Version-pinned environments should be generated after the final workflow has been successfully executed and validated.

## 7. Reproducibility levels

The repository supports different levels of reproducibility:

### Level 1: Methodological inspection

Users can inspect documentation, notebooks, and source code without downloading large datasets.

### Level 2: Lightweight execution

Users can execute selected notebooks using small sample data, if included.

### Level 3: Full workflow reproduction

Users can reconstruct the full dataset and execute the complete preprocessing, training, evaluation, and mapping workflow.

Level 3 reproduction requires access to the complete input dataset and adequate computational resources.

## 8. Validation and leakage control

Spatial-temporal modeling is vulnerable to inflated performance if data partitioning is poorly designed.

The final workflow must explicitly document:

- training data;
- validation data;
- test data;
- temporal holdout logic;
- spatial holdout logic, if used;
- normalization parameters estimated from training data only.

Random patch-level splitting should be avoided unless its limitations are explicitly acknowledged and controlled.

## 9. Expected reviewer use

A reviewer should be able to use this repository to:

- understand the complete computational workflow;
- inspect the model architecture and preprocessing logic;
- verify that data leakage controls were considered;
- reproduce selected outputs when data are available;
- evaluate whether the reported results are methodologically traceable.

## 10. Current status

This repository is under active development.

At this stage, the repository contains the initial reproducibility structure. Final notebooks, source code, configuration files, and output-generation scripts will be added as the manuscript workflow is consolidated.

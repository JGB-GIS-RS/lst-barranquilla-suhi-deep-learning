# lst-barranquilla-suhi-deep-learning

Deep learning workflow for modeling Surface Urban Heat Island (SUHI) patterns in Barranquilla, Colombia, using Landsat-derived spectral indices and land surface temperature.

## Overview

This repository supports the computational workflow of a remote sensing study focused on the spatial-temporal modeling of surface urban heat patterns in Barranquilla, Colombia.

The study uses Landsat Collection 2 Level-2 products to derive land surface temperature (LST) and spectral indices. These variables are organized as multi-temporal tensors and used to train and evaluate deep learning models for spatial-temporal LST modeling.

The repository is intended to improve transparency, reproducibility, and methodological traceability. It does not store large satellite imagery, raster stacks, tensors, or trained model checkpoints.

## Study area

The study area corresponds to Barranquilla, Colombia, and its surrounding urban and peri-urban environment. The analysis focuses on surface thermal behavior and its relationship with spectral indicators derived from Landsat imagery.

## Main methodological components

The workflow is organized around the following components:

1. Landsat image inventory and metadata organization.
2. Cloud, shadow, and invalid-pixel masking.
3. LST extraction from Landsat Collection 2 Level-2 products.
4. Computation of spectral indices.
5. Construction of multi-temporal tensors.
6. Spatial patch extraction.
7. Baseline model implementation.
8. Deep learning model training.
9. Model evaluation using spatial and statistical metrics.
10. Generation of prediction maps and spatial diagnostics.

## Repository structure

```text
lst-barranquilla-suhi-deep-learning/
│
├── configs/      Configuration files for paths, models, training, and experiments.
├── data/         Data documentation and reconstruction instructions.
├── docs/         Methodological and reproducibility documentation.
├── notebooks/    Sequential notebooks for the computational workflow.
├── outputs/      Documentation of expected outputs.
├── src/          Reusable Python source code.
│
├── .gitignore
├── LICENSE
└── README.md

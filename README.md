# lst-barranquilla-suhi-deep-learning

Computational workflow for modeling land surface temperature and Surface Urban Heat Island (SUHI) patterns in Barranquilla, Colombia, using Landsat 8/9 Collection 2 Level-2 products, spectral indices, and deep learning models.

## Overview

This repository supports the computational workflow of a remote sensing study focused on spatial-temporal modeling of land surface temperature (LST) and surface urban heat patterns in Barranquilla, Colombia.

The workflow uses Landsat 8/9 Collection 2 Level-2 products to derive LST and spectral indices. These variables are organized into multi-temporal datasets for subsequent modeling using baseline approaches and deep learning architectures.

The repository is intended to improve transparency, reproducibility, and methodological traceability. It does not store large satellite images, raster stacks, tensors, patch datasets, or trained model checkpoints.

## Study area

The study area corresponds to Barranquilla, Colombia, and its surrounding urban and peri-urban environment.

The Landsat WRS-2 reference used for the scene inventory is:

```
Path: 9
Row: 52
```

## Temporal scope

The operational period of the Landsat 8/9 workflow is:

```
2013–2025
```

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

## Main methodological components

The workflow is organized around the following components:

1. Landsat 8/9 scene inventory and metadata organization.
2. Quality masking and valid-pixel control.
3. LST extraction from Landsat Collection 2 Level-2 products.
4. Computation of Landsat-derived spectral indices.
5. Construction of multi-temporal datasets.
6. Spatial-temporal patch extraction.
7. Baseline model implementation.
8. Deep learning model training.
9. Model evaluation using statistical and spatial metrics.
10. Generation of prediction maps and spatial diagnostics.

## Repository structure

```
lst-barranquilla-suhi-deep-learning/
│
├── configs/      Configuration files for paths, models, training, and experiments.
├── data/         Data documentation and lightweight tabular products.
├── docs/         Methodological and reproducibility documentation.
├── notebooks/    Sequential notebooks for the computational workflow.
├── outputs/      Documentation of expected outputs.
├── src/          Reusable Python source code.
│
├── .gitignore
├── CITATION.cff
├── LICENSE
├── README.md
├── environment.yml
└── requirements.txt
```

## Current notebook sequence

The current notebook sequence is documented in:

```
notebooks/notebook_index.md
```

The first notebook available in the repository is:

```
notebooks/01_scene_inventory_landsat_8_9.ipynb
```

This notebook documents the Landsat 8/9 scene inventory for the 2013–2025 operational period.

## Data availability

Large geospatial datasets are not stored in this repository.

The following files are excluded from version control:

* original Landsat scenes;
* full-resolution raster stacks;
* GeoTIFF outputs;
* tensor datasets;
* patch datasets;
* trained model weights;
* temporary files generated during preprocessing or training.

Lightweight tabular products, such as scene inventories and summary tables, may be included when they improve transparency and reproducibility.

## Computational environment

The workflow is designed for Python-based geospatial and deep learning processing. Environment specifications are provided through:

* `requirements.txt`
* `environment.yml`

The notebooks may be executed locally, in JupyterLab, or in cloud-based environments such as Google Colab, depending on data availability and computational resources.

## Reproducibility notes

Full reproduction of the workflow requires access to the input satellite imagery, preprocessing outputs, and adequate computational resources.

The repository provides code, configuration files, documentation, notebooks, and lightweight tabular products to support methodological inspection and partial reproducibility.

Some notebooks may require access to Google Earth Engine, Google Drive, or external geospatial datasets. Users should configure their own local or cloud paths using the example files provided in the `configs/` directory.

## License

The code in this repository is released under the MIT License.

## Citation

Citation information will be updated once the associated manuscript is submitted or published.

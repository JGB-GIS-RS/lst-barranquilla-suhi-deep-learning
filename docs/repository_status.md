# Repository status

This document summarizes the current status of the repository and identifies pending components required for full reproducibility.

## 1. Current repository status

The repository currently contains a structured reproducibility framework for modeling land surface temperature and surface urban heat patterns in Barranquilla, Colombia.

The workflow is based on Landsat 8/9 Collection 2 Level-2 products for the 2013–2025 operational period.

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

At this stage, the repository supports methodological inspection, review of the scene inventory, and traceability of the intended preprocessing, modeling, training, and evaluation workflow.

## 2. Included components

The repository currently includes:

* general project documentation;
* methodological workflow documentation;
* data source documentation;
* preprocessing documentation;
* modeling documentation;
* reproducibility notes;
* repository status documentation;
* configuration templates;
* environment files;
* source-code module structure;
* notebook execution index;
* Landsat 8/9 scene inventory notebook;
* lightweight Landsat 8/9 scene inventory tables.

## 3. Completed components

The following components have been created:

* `README.md`
* `LICENSE`
* `CITATION.cff`
* `.gitignore`
* `requirements.txt`
* `environment.yml`
* `configs/`
* `data/`
* `data/scene_inventory/`
* `docs/`
* `notebooks/`
* `src/`

The following documentation files have been updated:

* `docs/data_sources.md`
* `docs/preprocessing.md`
* `docs/modeling.md`
* `docs/workflow.md`
* `docs/reproducibility_notes.md`
* `docs/repository_status.md`

The following configuration files have been updated:

* `configs/experiment_metadata.yml`
* `configs/model_config.yml`
* `configs/training_config.yml`
* `configs/paths_example.yml`

The following notebook is currently included:

* `notebooks/01_scene_inventory_landsat_8_9.ipynb`

The following lightweight data products are currently included:

* `data/scene_inventory/scene_inventory_landsat89_2013_2025.csv`
* `data/scene_inventory/annual_scene_count_landsat89_2013_2025.csv`
* `data/scene_inventory/monthly_scene_count_landsat89_2013_2025.csv`

## 4. Pending components

The following components are still pending:

* final study area definition file or documentation;
* executable preprocessing notebooks;
* quality-control notebooks;
* tensor construction notebooks;
* patch extraction notebooks;
* climate forcing integration notebooks or scripts;
* baseline model notebooks;
* deep learning model training notebooks;
* evaluation notebooks;
* spatial diagnostics notebooks;
* final source-code modules;
* final model hyperparameters;
* final train, validation, and test partitions;
* final version-pinned computational environment;
* final manuscript citation.

## 5. Reproducibility status

At this stage, the repository supports:

* methodological inspection;
* review of the Landsat 8/9 scene inventory;
* review of the 2013–2025 operational period;
* review of the exclusion of 2026;
* review of configuration files;
* review of the intended modeling and validation strategy.

The repository does not yet support full workflow reproduction.

Full reproducibility will require:

* final executable notebooks;
* reusable source-code modules;
* documented data reconstruction;
* final configuration files with validated parameters;
* external access to large input data;
* adequate computational resources.

## 6. Data status

Large geospatial datasets are not stored in this repository.

The following files must remain excluded from GitHub:

* original Landsat scenes;
* full-resolution raster stacks;
* GeoTIFF outputs;
* temporal tensors;
* patch datasets;
* model checkpoints;
* trained model weights;
* large prediction maps;
* temporary processing outputs.

Lightweight tabular products are allowed when they improve transparency and reproducibility.

The current lightweight data products are stored in:

```
data/scene_inventory/
```

## 7. Modeling status

The modeling documentation and configuration files currently define the intended modeling framework.

The intended formulation is:

```
T1 = Y - 3
T2 = Y - 2
T3 = Y - 1
Target = LST(Y)
```

The proposed model family is based on:

* U-Net;
* ConvLSTM;
* squeeze-and-excitation channel attention;
* interannual climate forcings in the final configuration.

The final executed models, hyperparameters, training logs, and evaluation results are still pending.

## 8. Reviewer interpretation

This repository should currently be interpreted as a structured and actively curated reproducibility framework under development.

It should not yet be interpreted as the final computational archive of the manuscript.

The included notebook and CSV files document the Landsat 8/9 scene inventory, but the full preprocessing, tensor construction, model training, and evaluation workflows are still under consolidation.

## 9. Next development steps

The next development steps are:

1. Add the final study area documentation or boundary reconstruction instructions.
2. Add executable preprocessing notebooks.
3. Add quality-control notebooks.
4. Add temporal dataset and tensor construction notebooks.
5. Add patch extraction notebooks.
6. Add climate forcing integration workflow.
7. Add model training notebooks.
8. Add evaluation and spatial diagnostics notebooks.
9. Add reusable Python source-code modules.
10. Validate and update the computational environment files.
11. Update citation information after manuscript submission or publication.

## 10. Status statement

The repository is currently suitable for methodological review and inspection of the initial Landsat 8/9 data inventory.

The repository is not yet a complete executable reproduction package.

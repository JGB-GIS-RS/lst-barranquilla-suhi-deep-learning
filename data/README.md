# Data

This directory documents the data sources and reconstruction workflow used in the study.

The repository does not store large geospatial datasets. It may include lightweight tabular products that improve transparency and reproducibility, such as scene inventories and summary tables.

## Data scope

The study uses Landsat 8/9 Collection 2 Level-2 products to derive land surface temperature and spectral indices for the Barranquilla study area.

The operational period of the workflow is:

```
2013–2025
```

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

The Landsat WRS-2 reference used for the scene inventory is:

```
Path: 9
Row: 52
```

## Directory structure

The expected structure of this directory is:

```
data/
│
├── README.md
└── scene_inventory/
    ├── scene_inventory_landsat89_2013_2025.csv
    ├── annual_scene_count_landsat89_2013_2025.csv
    └── monthly_scene_count_landsat89_2013_2025.csv
```

## Scene inventory

The `scene_inventory/` directory may contain lightweight CSV files derived from the Landsat 8/9 scene inventory.

These files are included to allow reviewers and users to inspect the temporal availability of the input scenes without downloading large raster datasets.

Expected files include:

* `scene_inventory_landsat89_2013_2025.csv`: scene-level inventory.
* `annual_scene_count_landsat89_2013_2025.csv`: annual scene count by sensor.
* `monthly_scene_count_landsat89_2013_2025.csv`: monthly scene count by year and sensor.

## Data not stored in this repository

Large geospatial datasets are not stored in this GitHub repository. This includes:

* original Landsat scenes;
* raster stacks;
* full-resolution GeoTIFF outputs;
* tensor datasets;
* patch datasets;
* trained model weights;
* model checkpoints;
* large prediction maps;
* temporary preprocessing outputs.

These files should be reconstructed from the documented workflow or stored externally using an appropriate storage system.

## Reconstruction workflow

Data reconstruction instructions are documented in:

```
docs/data_sources.md
docs/preprocessing.md
notebooks/notebook_index.md
```

The first notebook associated with this directory is:

```
notebooks/01_scene_inventory_landsat_8_9.ipynb
```

This notebook documents the construction of the Landsat 8/9 scene inventory for the 2013–2025 operational period.

## Sample data

A small sample dataset may be included later only for testing the computational workflow.

Any sample dataset must be clearly identified as a reduced demonstration dataset and must not be interpreted as the full dataset used in the manuscript.

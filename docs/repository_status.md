# Repository status

This document summarizes the current status of the repository and identifies pending components required for full reproducibility.

## Current repository status

The repository currently contains the initial structure required to support a reproducible remote sensing and deep learning workflow for modeling land surface temperature and surface urban heat patterns in Barranquilla, Colombia.

The repository includes:

- general project documentation;
- methodological workflow documentation;
- data source documentation;
- preprocessing documentation;
- modeling documentation;
- reproducibility notes;
- configuration templates;
- environment files;
- source-code module structure;
- notebook execution index.

## Completed components

The following components have been created:

- `README.md`
- `LICENSE`
- `CITATION.cff`
- `.gitignore`
- `requirements.txt`
- `environment.yml`
- `configs/`
- `data/`
- `docs/`
- `notebooks/`
- `src/`

## Pending components

The following components are still pending:

- final Landsat scene inventory;
- final study area definition file;
- executable preprocessing notebooks;
- quality-control notebooks;
- tensor construction notebooks;
- patch extraction notebooks;
- baseline model notebooks;
- deep learning model training notebook;
- evaluation notebook;
- spatial diagnostics notebook;
- final source-code modules;
- sample dataset for lightweight testing;
- final version-pinned computational environment;
- final manuscript citation.

## Reproducibility status

At this stage, the repository supports methodological inspection but not yet full workflow reproduction.

Full reproducibility will require:

- final notebooks;
- executable source code;
- documented data reconstruction;
- configuration files with final parameters;
- external access to large input data;
- adequate computational resources.

## Data status

Large geospatial datasets are not stored in this repository.

The following files should remain excluded from GitHub:

- Landsat scenes;
- raster stacks;
- GeoTIFF outputs;
- temporal tensors;
- patch datasets;
- model checkpoints;
- large prediction maps.

## Reviewer interpretation

This repository should currently be interpreted as a structured reproducibility framework under active development.

It should not yet be interpreted as the final computational archive of the manuscript.

## Next development steps

The next steps are:

1. Add the final notebook sequence.
2. Add reusable Python source-code modules.
3. Add final configuration files.
4. Add data reconstruction instructions.
5. Add lightweight sample data, if feasible.
6. Validate the environment files.
7. Update citation information after manuscript submission or publication.

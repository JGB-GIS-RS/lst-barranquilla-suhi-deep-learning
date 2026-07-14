# Configuration reference

This directory contains YAML files that document the principal paths, model
settings, training parameters, data partitions, and experiment metadata used in
the study.

These files provide a structured reference for methodological inspection and
traceability. The current notebooks are self-contained and do not automatically
load these YAML files at runtime. Therefore, the YAML files should be interpreted
as configuration records rather than as an executable configuration system.

Included files:

- `paths_example.yml`: example structure for adapting external data and output
  paths to a local, cloud, or Google Colab environment.
- `model_config.yml`: summary of the M1–M5 architectures, inputs, and principal
  model settings.
- `training_config.yml`: summary of the loss function, optimizer, learning-rate
  control, early stopping, batch sizes, temporal partitions, normalization, and
  leakage-control criteria.
- `experiment_metadata.yml`: structured metadata describing the study area,
  satellite and climate-radiative data, temporal scope, modeling task, and
  repository status.

The notebooks remain the authoritative implementation of the computational
workflow. If a discrepancy exists between a YAML record and a notebook, the
executed notebook and the methodological description in the manuscript take
precedence.

# Patch diagnostic figures

This directory contains lightweight visual quality-control products associated
with the T3 patch-extraction workflow implemented in:

```text
notebooks/07_patch_extraction.ipynb
```

The figures support inspection of the spatial sampling stage used to construct
training, validation, and test datasets from the multi-temporal tensors generated
in Notebook 06.

## Patch design

The documented patch configuration is:

```text
Patch size:              128 x 128 pixels
Training stride:          64 pixels
Validation stride:       128 pixels
Test stride:             128 pixels
Minimum valid fraction:  0.70
```

At 30 m spatial resolution, each patch covers approximately:

```text
3.84 km x 3.84 km
```

Training patches overlap because the stride is smaller than the patch size.
Validation and test patches use non-overlapping extraction under the documented
configuration.

## Temporal assignment

Patches are assigned to data partitions according to the target year before model
training:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

Patch-level random reassignment across these temporal partitions is not used.

## Documented diagnostics

Depending on the executed notebook outputs, the figures may show:

- patch footprints over the study domain;
- accepted and rejected patch locations;
- valid-pixel fraction by patch;
- spatial sampling density;
- overlap patterns in the training partition;
- target-year and split-specific patch distributions;
- examples of spectral, target, and validity-mask arrays;
- edge effects and domain-boundary exclusions.

## Interpretation

The figures are intended to verify that patch extraction preserves the declared
temporal partition, spatial geometry, and minimum-validity threshold.

The binary validity mask is used to identify pixels eligible for loss and metric
calculation. It is not included as an explanatory predictor.

A patch may be rejected when its valid fraction falls below the configured
threshold because of water, unavailable observations, invalid pixels, or domain
boundaries.

The use of overlapping training patches increases the number of training samples,
but it does not create independent spatial observations. Consequently, the
reported evaluation should be interpreted as a temporal holdout over a shared
geographic domain, not as an independent spatial-block validation.

## Reference-experiment archives

The public tensor- and patch-construction notebooks document the reconstruction
logic. The model-training notebooks consume archived external patch datasets from
the reference experiment.

Historical archive names include:

```text
T3_full
T3_full_M5B_delta_t2m_delta_radiation
```

Minor differences in patch totals may occur among historical reconstruction
versions because of edge handling, mask acceptance, or intermediate archive
versioning. The archived datasets loaded by the training notebooks define the
reference experiment reported in the model-comparison outputs.

These differences do not change the declared variables, T3 temporal formulation,
target-year partitions, patch dimensions, or evaluation protocol.

## Reproducibility boundary

Complete patch arrays are intentionally excluded from GitHub because of their
size.

The figures in this directory provide visual traceability but do not replace the
external tensor and patch archives required for full retraining.

These diagnostics should not be interpreted as evidence of model accuracy or as
an independent validation dataset.

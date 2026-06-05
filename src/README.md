# Source code

This directory contains reusable Python modules used by the notebooks.

The objective of this directory is to keep notebooks concise, auditable, and reproducible by moving repeated procedures into documented functions.

## Planned module structure

The source code will be organized into the following submodules:

```text
src/
│
├── preprocessing/
│   ├── masks.py
│   ├── indices.py
│   ├── lst.py
│   └── alignment.py
│
├── tensors/
│   ├── build_tensors.py
│   └── patch_extraction.py
│
├── models/
│   ├── baselines.py
│   ├── unet.py
│   ├── convlstm.py
│   └── unet_convlstm_se.py
│
├── training/
│   ├── train.py
│   ├── losses.py
│   └── callbacks.py
│
├── evaluation/
│   ├── metrics.py
│   ├── residuals.py
│   └── diagnostics.py
│
└── visualization/
    ├── maps.py
    └── plots.py
```

## Coding principles

The source code should follow these principles:

1. Avoid hard-coded personal paths.
2. Use configuration files whenever possible.
3. Keep functions small and testable.
4. Document inputs and outputs.
5. Separate preprocessing, modeling, evaluation, and visualization logic.
6. Avoid duplicating code across notebooks.
7. Preserve consistency between code, notebooks, and manuscript methods.

## Current status

This directory currently defines the intended source-code organization. Final Python modules will be added after the computational workflow is consolidated.

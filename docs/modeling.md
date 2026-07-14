# Modeling

This document describes the implemented retrospective deep-learning workflow for
annual land surface temperature (LST) estimation in the Barranquilla Metropolitan
Area, Colombia.

The public repository documents the T3 temporal formulation, the M1-M5 model
family, model training, comparative evaluation, conversion of normalized errors
to physical units, and spatial diagnostics for the retained model.

## 1. Public modeling scope

The notebooks included in this repository cover:

- construction of T3 annual predictor tensors;
- extraction and masking of spatial patches;
- training of models M1-M5;
- evaluation by temporal split and target year;
- comparison of normalized and physical-unit performance;
- full-domain and local spatial diagnostics for M5 when the required external
  rasters are available.

The current public release documents the retrospective modeling component. It
does not implement the CA-ANN/MOLUSCE land-cover simulation, prospective
spectral-predictor generation, or conditioned 2035 LST/SUHI projection described
as a separate component of the broader study.

## 2. Inferential interpretation

The modeling task is supervised spatial-temporal regression.

For models M1-M4, annual LST for a target year `Y` is estimated exclusively from
three antecedent Landsat spectral states:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

M5 uses the same antecedent spectral sequence and two regional
climate-radiative descriptors associated with the target year. Consequently, M5
is interpreted as a retrospective LST estimate conditioned on externally known
target-year covariates. It is not an autonomous operational forecast based only
on information available before year `Y`.

The target-year Landsat LST is used only as the supervised response for loss and
metric calculation. It is never included as a predictor.

## 3. Temporal formulation and partition

The operational Landsat period is 2013-2025. Because each target requires three
antecedent annual states, the modeled target years are 2016-2025.

| Split | Target years | Antecedent states |
|---|---|---|
| Training | 2016-2019 | 2013-2018, arranged in rolling T3 windows |
| Validation | 2020-2022 | 2017-2021, arranged in rolling T3 windows |
| Test | 2023-2025 | 2020-2024, arranged in rolling T3 windows |

The split is assigned by target year and remains fixed across the evaluated
configurations. Patches are not randomly reassigned across temporal partitions.

This design is a temporally controlled retrospective evaluation. It is not a
spatial block holdout, because training, validation, and test patches may cover
the same geographic domain in different target years.

## 4. Predictor and target variables

The spectral predictor set contains six annual Landsat-derived indices:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

For each target year, the six indices are extracted from three antecedent states:

```text
3 temporal states x 6 indices = 18 spectral channels
```

M5 additionally uses:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These NASA POWER variables are annual regional descriptors derived for the
model-domain centroid. They are replicated spatially inside each patch only for
tensor compatibility and must not be interpreted as climate fields distributed at
30 m resolution.

The target is annual Landsat-derived LST. During training, LST is represented by
a global z-score using parameters estimated only from training targets. Spectral
indices are transformed using robust train-only percentile limits. The stored
training parameters are applied unchanged to validation and test data.

## 5. Patch representation and masks

The implemented patch geometry is:

```text
Patch size: 128 x 128 pixels
Training stride: 64 pixels
Validation stride: 128 pixels
Test stride: 128 pixels
Minimum valid fraction: 0.70
```

Training patches therefore overlap, whereas validation and test patches use a
systematic non-overlapping grid.

Each sample includes:

```text
X: predictor tensor
y: normalized target LST
mask: binary valid-pixel mask
target_year: target-year identifier
```

The valid mask restricts optimization and evaluation to terrestrial pixels with
valid antecedent predictors and valid target-year LST. Invalid, water,
out-of-domain, and unavailable pixels remain inside the rectangular patch but do
not contribute to the loss or metrics.

Large tensor and patch archives are external to GitHub. The training notebooks
load the experiment archives expected by each model family and validate channel
counts, tensor orientation, target years, and mask compatibility before training.

## 6. Tensor organization

### M1 and M2

For U-Net and SE U-Net, the T3 sequence is represented as an implicit
multichannel stack:

```text
batch x 18 x 128 x 128
```

### M3 and M4

For ConvLSTM-based spectral models, the same 18 channels are reorganized as an
explicit temporal sequence:

```text
batch x 3 x 6 x 128 x 128
```

No additional predictor information is introduced by this reshape.

### M5

The external patch archive contains:

```text
batch x 20 x 128 x 128
```

comprising 18 spectral channels and two regional climate-radiative channels.

The implementation separates these inputs internally:

1. the 18 spectral channels are reshaped to `3 x 6` and processed by ConvLSTM;
2. the final ConvLSTM hidden state contains 32 channels;
3. temporal squeeze-and-excitation recalibrates that hidden representation;
4. the two regional descriptors are concatenated after temporal encoding;
5. the resulting 34-channel tensor enters the spatial SE U-Net.

Therefore, the raw M5 sample contains 20 channels, but the spatial U-Net receives
a fused 34-channel representation:

```text
32 temporal latent channels + 2 regional descriptors = 34 channels
```

## 7. Evaluated model configurations

| Model | Public label | Predictor structure | Temporal representation | SE modules | Methodological role |
|---|---|---|---|---|---|
| M1 | U-Net baseline | 18 spectral channels | Implicit stacking | No | Spatial encoder-decoder reference |
| M2 | SE U-Net | 18 spectral channels | Implicit stacking | Spatial SE | Tests channel recalibration under the M1 input |
| M3 | ConvLSTM U-Net | 3 x 6 spectral sequence | Explicit ConvLSTM | No | Tests explicit temporal encoding |
| M4 | ConvLSTM-SE U-Net | 3 x 6 spectral sequence | Explicit ConvLSTM | Temporal and spatial SE | Tests SE within a recurrent configuration |
| M5 | T3-Climate ConvLSTM-SE U-Net | 18 spectral + 2 regional descriptors | Explicit spectral ConvLSTM plus auxiliary fusion | Temporal and spatial SE | Final augmented configuration |

The historical identifier `M5B` appears in some external folder names and
notebook metadata. The public manuscript label is M5.

## 8. Architecture

The common spatial backbone is a U-Net-like encoder-decoder with:

- base width of 32 channels;
- three downsampling stages and a bottleneck;
- transposed-convolution upsampling;
- encoder-decoder skip concatenations;
- a linear `1 x 1` output convolution;
- dropout of 0.10.

ConvLSTM models use a 32-channel hidden representation with convolutional gates,
preserving patch geometry during temporal encoding.

SE blocks perform channel-wise recalibration. Their activations are interpreted
as internal feature modulation, not as causal importance, independent variable
importance, or direct biophysical attribution.

## 9. Training protocol

The common optimization settings are:

```text
Optimizer: AdamW
Initial learning rate: 1e-3
Weight decay: 1e-5
Loss: masked Huber loss
Huber delta: 1.0
Gradient clipping norm: 1.0
Scheduler: ReduceLROnPlateau
Scheduler factor: 0.5
Scheduler patience: 5 epochs
Checkpoint criterion: minimum validation loss
```

Model-specific settings are:

| Configuration | Batch size | Maximum epochs | Early-stopping patience | Seed |
|---|---:|---:|---:|---:|
| M1-M4 | 4 | 100 | 15 | 42 |
| M5 | 8 | 120 | 18 | 20260530 |

M5 therefore differs from M4 in both predictor set and training protocol. The
M4-M5 contrast must be interpreted as a comparison between complete
experimental configurations, not as a pure ablation that isolates the effect of
the two regional descriptors.

The notebooks are designed for GPU execution in Google Colab-compatible
environments. Runtime and memory requirements depend on the external patch
archives and available hardware.

## 10. Loss and metric computation

The masked Huber loss is evaluated only over pixels for which `mask > 0`.

The evaluation notebooks accumulate sufficient statistics over all valid pixels
within each split or target year. RMSE, MAE, bias, and coefficient of determination
are therefore computed globally over the evaluated valid pixels rather than
averaged from independent batch-level metrics.

The reported diagnostics are:

```text
RMSE
MAE
Bias
R2
Number of valid pixels
```

Evaluation is exported:

- by temporal split;
- by target year;
- for the aggregated TEST period;
- in normalized scale;
- in degrees Celsius after inverse scaling.

## 11. Model-comparison logic

M1-M4 use the same spectral predictor content, masks, target variable, temporal
partition, normalization parameters, loss definition, optimizer family,
checkpoint criterion, and main evaluation metrics. Their comparison therefore
supports interpretation of architectural differences within the evaluated
protocol.

The sequence must not be interpreted as implying that architectural complexity
should produce monotonic improvement.

M5 is not a strict continuation of that architectural comparison. It introduces
target-year regional descriptors and uses a specific training configuration.
Accordingly:

- M1-M4 constitute the controlled spectral-model comparison;
- M4-M5 compare complete configurations;
- the M4-M5 difference cannot be attributed exclusively to climate-radiative
  augmentation;
- M5 performance does not demonstrate causal influence of either NASA POWER
  variable.

Notebook `08F_compare_models_M1_M5.ipynb` consolidates the split-wise and
year-wise outputs from the five training notebooks and generates the comparative
tables and figures.

## 12. Physical-unit evaluation

Notebook `09_physical_unit_evaluation_and_exports_figures.ipynb` converts
normalized errors to degrees Celsius using the frozen training-only LST standard
deviation.

For the stored reference parameters:

```text
mu_train = 38.48322677612305 degrees Celsius
sigma_train = 4.032179355621338 degrees Celsius
```

The conversions are:

```text
RMSE_C = RMSE_normalized x sigma_train
MAE_C = MAE_normalized x sigma_train
Bias_C = Bias_normalized x sigma_train
```

`R2` is unchanged when the same linear inverse transformation is applied to the
observed and predicted values.

The notebook exports model rankings, year-wise TEST diagnostics, and
manuscript-oriented comparison figures.

## 13. Spatial diagnostics

When the required external full-domain rasters are available, Notebook 09 can:

- read normalized observed and predicted M5 rasters;
- apply inverse z-score normalization;
- export observed, predicted, and residual GeoTIFFs in degrees Celsius;
- calculate full-domain RMSE, MAE, bias, `R2`, and Pearson correlation;
- generate observed-predicted agreement graphics;
- produce residual maps;
- produce selected local zoom-window diagnostics.

Residuals are defined as:

```text
predicted LST - observed LST
```

The local zoom windows are illustrative spatial diagnostics. They are not
independent validation subsets and must not be interpreted as additional
holdouts.

The full-domain mosaic metrics are also not numerically interchangeable with
metrics calculated from the non-overlapping TEST patch archive, because the
mosaic may be assembled from overlapping windows and spatial weighting.

## 14. Leakage control

The implemented workflow applies the following controls:

- target-year partitions are fixed before model training;
- LST scaling parameters are estimated only from training targets;
- spectral scaling parameters are estimated only from training predictors;
- scaling parameters are frozen for validation and test;
- target-year LST is not used as an input;
- antecedent LST maps are not used as predictors;
- model predictions and residuals are not predictors;
- validation controls learning-rate scheduling, checkpoint selection, and early
  stopping;
- TEST is reserved for final retrospective evaluation and is not used for
  checkpoint selection or hyperparameter optimization.

The NASA POWER descriptors used by M5 are associated with the target year. Their
use does not introduce target LST into the input, but it changes the inferential
meaning of M5 from antecedent-only estimation to estimation conditioned on known
or prescribed external covariates.

## 15. Limitations

The modeling design has several explicit limitations:

- the annual composites do not represent daily or intra-seasonal thermal
  variability;
- the temporal split is not an independent spatial-block validation;
- spatial autocorrelation may increase similarity among patches from the same
  domain;
- training overlap increases sample density but not the number of independent
  geographic regions;
- NASA POWER descriptors do not represent intra-urban climate variability;
- M5 cannot operate prospectively without prescribed target-year
  climate-radiative assumptions;
- M4-M5 is not a pure climate ablation;
- SE responses are not causal explanations;
- convolutional regression can smooth local thermal extremes and compress the
  observed temperature range;
- full reproduction requires external tensors, patch archives, checkpoints, and
  full-resolution rasters.

## 16. Reproducibility boundary

The authoritative implementations are:

```text
notebooks/07_patch_extraction.ipynb
notebooks/08A_train_M1_unet_baseline.ipynb
notebooks/08B_train_M2_se_unet.ipynb
notebooks/08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
notebooks/08D_train_M4_convlstm_se_unet.ipynb
notebooks/08E_train_M5_t3_climate_convlstm_se_unet.ipynb
notebooks/08F_compare_models_M1_M5.ipynb
notebooks/09_physical_unit_evaluation_and_exports_figures.ipynb
```

The YAML files under `configs/` provide structured configuration references, but
the notebooks contain the executed implementations and take precedence if a
discrepancy is found.

The public repository supports methodological inspection, traceability, and
partial reproducibility. Complete execution requires the external large-volume
products and adequate computational resources.

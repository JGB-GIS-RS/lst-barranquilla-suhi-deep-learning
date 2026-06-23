# Modeling

This document describes the modeling strategy used for spatial-temporal land surface temperature prediction in Barranquilla, Colombia.

The workflow follows a progressive modeling design. First, several spectral-only deep learning models are compared under identical data conditions. Then, the final climate-augmented configuration incorporates interannual NASA POWER climate descriptors to evaluate whether annual climate context improves prediction after the best spectral-temporal architecture has been established.

## 1. Modeling objective

The modeling task is formulated as a supervised spatial-temporal regression problem.

The objective is to estimate annual land surface temperature (LST) from multi-temporal sequences of Landsat-derived spectral variables and, in the final model configuration, from additional interannual climate descriptors derived from NASA POWER.

The target variable is:

* annual Landsat-derived land surface temperature, represented as train-only normalized LST z-score during model training.

The main predictor variables are:

* Landsat-derived annual spectral indices;
* interannual NASA POWER climate descriptors in the final climate-augmented model.

The model is designed to learn the relationship between antecedent surface conditions, interannual climate context, and the spatial distribution of LST in the target year.

## 2. Temporal formulation

The modeling framework uses a three-year antecedent window, referred to as T3, to predict the LST of a target year.

The temporal formulation is:

```text
T1 = Y - 3
T2 = Y - 2
T3 = Y - 1
Target = LST(Y)
```

For a given target year `Y`, the model uses spectral information from `Y−3`, `Y−2`, and `Y−1`. The observed LST of the target year `Y` is not used as an input predictor.

For example:

| Target year Y | Antecedent years used as input |
| ------------: | ------------------------------ |
|          2016 | 2013, 2014, 2015               |
|          2017 | 2014, 2015, 2016               |
|          2018 | 2015, 2016, 2017               |
|          2019 | 2016, 2017, 2018               |
|          2020 | 2017, 2018, 2019               |
|          2021 | 2018, 2019, 2020               |
|          2022 | 2019, 2020, 2021               |
|          2023 | 2020, 2021, 2022               |
|          2024 | 2021, 2022, 2023               |
|          2025 | 2022, 2023, 2024               |

This formulation allows the network to represent antecedent temporal conditions before the prediction year while preserving the spatial structure of urban thermal patterns.

## 3. Data partitioning

The operational modeling period is 2013–2025.

The target years are 2016–2025, because each target year requires three antecedent years.

The temporal split is:

| Split      | Target years           |
| ---------- | ---------------------- |
| Training   | 2016, 2017, 2018, 2019 |
| Validation | 2020, 2021, 2022       |
| Test       | 2023, 2024, 2025       |

The year 2026 is excluded from the final annual modeling workflow because it does not represent a complete annual period.

The temporal split is kept fixed across comparable model configurations. This avoids temporal leakage and allows model differences to be interpreted under a consistent validation design.

## 4. Input variables

The spectral predictors are annual Landsat-derived indices calculated from surface reflectance.

The retained spectral variables are:

* NDVI;
* NDMI;
* NDBI;
* UI;
* SAVI;
* BSI.

For each target year, these six indices are extracted for the three antecedent years. Therefore, the spectral-only T3 tensor contains:

```text
3 antecedent years × 6 spectral indices = 18 spectral channels
```

The climate-augmented tensor adds two NASA POWER interannual climate descriptors:

* `delta_t2m_mean_Y_minus_Yminus1`;
* `delta_solar_radiation_mean_Y_minus_Yminus1`.

These descriptors represent the interannual change between the target year `Y` and the immediately preceding year `Y−1`.

Therefore, the final climate-augmented tensor contains:

```text
18 spectral channels + 2 climate channels = 20 input channels
```

The two climate channels are annual regional descriptors associated with the model-domain centroid. They are replicated spatially as constant layers only to make them compatible with convolutional tensor processing. They must not be interpreted as spatially distributed climate rasters.

## 5. Conceptual tensor structure

The spectral-only tensor contains the antecedent Landsat spectral sequence.

For U-Net and SE U-Net models, the temporal dimension is represented implicitly by stacking all antecedent spectral maps as channels:

```text
batch × 18 × patch_height × patch_width
```

For ConvLSTM-based models, the same spectral information is reorganized explicitly as a temporal sequence:

```text
batch × 3 × 6 × patch_height × patch_width
```

where:

```text
3 = antecedent years: Y−3, Y−2, Y−1
6 = spectral indices per year
```

The final climate-augmented tensor contains the same spectral T3 information plus two annual climate channels:

```text
batch × 20 × patch_height × patch_width
```

Conceptually, this corresponds to:

```text
18 spectral T3 channels + 2 annual climate channels
```

During model implementation, the spectral channels may be reshaped into a temporal ConvLSTM input, while the two climate channels may be handled as auxiliary spatially replicated predictors.

The target tensor is:

```text
batch × 1 × patch_height × patch_width
```

The associated mask tensor defines valid pixels used for loss and metric calculation.

## 6. Climate forcing variables

The final model configuration incorporates two interannual climate forcing variables derived from NASA POWER Daily API.

NASA POWER variables were first processed as annual regional descriptors associated with the model-domain centroid. Candidate descriptors included annual temperature, solar radiation, precipitation, relative humidity, wind speed, anomalies, and interannual deltas.

The final retained variables are:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These variables were retained because they are physically interpretable and consistent with the T3 formulation. The model predicts the LST of year `Y` from antecedent surface conditions up to `Y−1`; therefore, interannual changes between `Y` and `Y−1` provide contextual information about annual atmospheric shifts not directly available in the antecedent spectral sequence.

The climate variables do not include observed LST from the target year, previous model predictions, or residuals. Their role is to provide annual climate context, not to replace satellite-derived spatial predictors.

## 7. Target variable

The target variable is annual land surface temperature derived from Landsat Collection 2 Level-2 products.

During training, LST is represented as a z-score normalized variable using parameters estimated from the training period only.

If model evaluation is reported in physical units, the inverse normalization must be applied before computing temperature-based metrics such as RMSE and MAE in degrees Celsius.

The target-year LST is never included as an input predictor.

## 8. Progressive model configurations

The workflow uses a progressive model comparison strategy.

The first four models use exactly the same spectral-only T3 input data. They differ only in architecture. This isolates the contribution of architectural components such as encoder-decoder representation, channel attention, and explicit temporal memory.

The final model introduces two NASA POWER interannual climate predictors. This allows the effect of climate augmentation to be evaluated separately from the effect of architecture.

| Model                                     | Input structure                           | Temporal representation                              | Attention | Climate predictors |
| ----------------------------------------- | ----------------------------------------- | ---------------------------------------------------- | --------- | ------------------ |
| Model 1: U-Net baseline                   | 18 spectral channels                      | Implicit temporal stacking                           | No        | No                 |
| Model 2: SE U-Net                         | 18 spectral channels                      | Implicit temporal stacking                           | Yes       | No                 |
| Model 3: ConvLSTM U-Net                   | 3 × 6 spectral channels                   | Explicit ConvLSTM sequence                           | No        | No                 |
| Model 4: ConvLSTM-SE U-Net                | 3 × 6 spectral channels                   | Explicit ConvLSTM sequence                           | Yes       | No                 |
| Final model: T3-Climate ConvLSTM-SE U-Net | 18 spectral channels + 2 climate channels | Explicit spectral sequence + auxiliary climate input | Yes       | Yes                |

The first four models use exactly the same:

* spectral T3 input data;
* spatial masks;
* target variable;
* train/validation/test years;
* normalization parameters;
* loss function;
* evaluation metrics;
* early-stopping criterion.

Therefore, their comparison isolates architectural effects.

The final T3-Climate ConvLSTM-SE U-Net model uses the best spectral-temporal architecture and adds two interannual climate predictors derived from NASA POWER. This design evaluates the added value of climate augmentation after the spectral-temporal architecture has been established.

## 9. Model sequence

The model sequence is:

1. U-Net baseline;
2. SE U-Net;
3. ConvLSTM U-Net;
4. ConvLSTM-SE U-Net;
5. T3-Climate ConvLSTM-SE U-Net.

This progressive comparison evaluates the contribution of:

* spatial encoder-decoder representation;
* channel-wise recalibration;
* explicit temporal memory;
* interannual climate context.

The sequence is designed to avoid conflating architecture improvements with predictor-set changes.

## 10. U-Net component

The U-Net-like structure is used to preserve multi-scale spatial information.

The encoder extracts spatial features at progressively coarser levels, while the decoder reconstructs the prediction at the original spatial resolution.

Skip connections allow the model to recover local spatial detail that may be lost during downsampling.

This component is important because urban thermal patterns are spatially structured and strongly influenced by surface heterogeneity.

## 11. ConvLSTM component

ConvLSTM layers are used to model temporal dependencies while preserving spatial structure.

Unlike fully connected recurrent layers, ConvLSTM operations maintain spatial neighborhoods through convolutional gates.

This is relevant for remote sensing problems because the temporal evolution of urban thermal patterns is spatially structured rather than independent at the pixel level.

In this workflow, ConvLSTM blocks process the antecedent sequence:

```text
Y - 3, Y - 2, Y - 1
```

and support the prediction of:

```text
LST(Y)
```

## 12. Channel attention component

Channel attention modules are used to recalibrate feature maps according to their relative contribution to the prediction task.

Squeeze-and-excitation mechanisms are one implementation of channel attention.

The inclusion of attention is interpreted cautiously. Attention weights indicate internal feature recalibration, but they are not equivalent to causal explanation or direct physical interpretability.

In this workflow, SE mechanisms are interpreted as channel-wise recalibration modules that allow the network to modulate internal spectral and climate-related representations during learning.

## 13. T3-Climate ConvLSTM-SE U-Net model

The final model is referred to as the T3-Climate ConvLSTM-SE U-Net model.

This model corresponds to the final climate-augmented configuration selected after the progressive evaluation of spectral-only architectures.

It combines:

* the T3 spectral formulation;
* explicit temporal modeling through ConvLSTM;
* channel-wise recalibration through SE modules;
* two interannual NASA POWER climate descriptors.

The final model does not use a different target variable, different masks, or different evaluation years. Its main difference from the spectral-only ConvLSTM-SE U-Net is the addition of two climate predictors.

## 14. Training strategy

The training strategy must report:

* training, validation, and test periods;
* input tensor dimensions;
* patch size;
* stride or overlap;
* batch size;
* number of epochs;
* optimizer;
* learning rate;
* loss function;
* early-stopping criteria;
* hardware used for training.

All training decisions must be reproducible through the notebooks, source code, or configuration files.

## 15. Leakage control

The modeling workflow must avoid information leakage between training, validation, and test datasets.

At minimum, the workflow ensures that:

* normalization parameters are estimated using training data only;
* target-year LST is not used as an input predictor;
* validation and test target years are not included in training;
* model comparison uses fixed partitions and masks;
* NASA POWER climate forcings do not encode observed LST;
* model predictions or residuals are not used as input predictors.

Leakage control is essential for defensible spatial-temporal prediction.

## 16. Loss function

The loss function is selected according to the regression objective.

Candidate loss functions include:

* mean squared error;
* mean absolute error;
* Huber loss.

In this workflow, masked loss functions are required because invalid pixels, water pixels, and non-study-domain pixels must not contribute to optimization.

If training is performed on normalized LST values, final evaluation can be transformed back to physical units for interpretation.

## 17. Evaluation metrics

Model performance is evaluated using statistical and spatial diagnostics.

Recommended metrics include:

* RMSE;
* MAE;
* coefficient of determination;
* bias;
* year-wise performance;
* split-wise performance;
* spatial residual patterns.

Numerical metrics alone are insufficient. Spatial residual maps are required to evaluate whether the model systematically fails in specific urban, peri-urban, vegetated, coastal, or water-adjacent areas.

## 18. Model comparison

Model comparison must be performed under equivalent conditions.

For the spectral-only model family, all models use the same:

* spectral input data;
* patches;
* masks;
* train/validation/test split;
* target variable;
* normalization parameters;
* loss function;
* evaluation metrics.

The final climate-augmented model intentionally changes the predictor set by adding two NASA POWER interannual descriptors. Therefore, it should be interpreted as a second-stage comparison: spectral-only best architecture versus climate-augmented final configuration.

A model with higher apparent accuracy but weaker validation design should not be interpreted as superior.

## 19. Prediction outputs

The trained model may be used to generate spatial LST prediction maps.

Expected outputs include:

* predicted LST maps;
* observed versus predicted maps;
* residual maps;
* error summary tables;
* annual diagnostics;
* spatial diagnostics;
* hotspot or high-temperature recurrence maps.

Large prediction outputs are not stored in this repository.

## 20. Uncertainty and limitations

The modeling workflow acknowledges the following limitations:

* Landsat temporal resolution limits the representation of short-term thermal dynamics.
* Cloud masking reduces the number of valid observations.
* Cross-sensor differences between Landsat 8 and Landsat 9 may introduce residual inconsistencies.
* LST is sensitive to acquisition time, surface moisture, atmospheric conditions, and land-cover state.
* NASA POWER climate forcings are regional annual descriptors and do not represent local microclimatic variability at 30 m resolution.
* Replicated climate channels are used for tensor compatibility and must not be interpreted as spatially distributed climate observations.
* Deep learning models may reproduce spatial patterns without necessarily explaining their physical causes.
* Apparent accuracy can be inflated by spatial autocorrelation if validation is poorly designed.

These limitations must be considered when interpreting model performance and spatial prediction outputs.

## 21. Reproducibility requirements

The final modeling workflow must provide:

* executable notebooks;
* reusable source code;
* configuration files;
* model parameter documentation;
* data partitioning metadata;
* environment specification;
* clear instructions for reproducing evaluation tables and figures.

The repository allows reviewers to inspect the computational logic, even if full model training requires external datasets and GPU resources.

## 22. Current status

This document reflects the current modeling structure of the repository.

Completed repository modules include:

* scene inventory;
* annual Landsat preprocessing;
* quality control;
* train-only normalization;
* NASA POWER climate forcing integration;
* T3-Climate tensor construction.

Subsequent modules will document:

* patch extraction;
* model training;
* model evaluation;
* spatial diagnostics;
* prediction outputs.

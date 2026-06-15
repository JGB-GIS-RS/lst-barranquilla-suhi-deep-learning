# Modeling

This document describes the modeling strategy used for spatial-temporal land surface temperature prediction in Barranquilla, Colombia.

## 1. Modeling objective

The modeling task is formulated as a supervised spatial-temporal regression problem.

The objective is to estimate annual land surface temperature from multi-temporal sequences of Landsat-derived spectral variables and, in the final configuration, interannual climate forcings.

The target variable is:

* land surface temperature.

The main input variables are:

* Landsat-derived spectral indices;
* interannual climate forcing variables, if retained in the final model configuration.

The model is designed to learn the relationship between antecedent spectral and climatic conditions and the spatial distribution of land surface temperature in the target year.

## 2. Temporal formulation

The modeling framework uses three antecedent temporal states to predict the land surface temperature of a target year.

The temporal formulation is:

```
T1 = Y - 3
T2 = Y - 2
T3 = Y - 1
Target = LST(Y)
```

Under this structure, the model does not use observed LST from the target year as an input variable.

This formulation allows the network to represent antecedent temporal conditions prior to the prediction year while preserving the spatial structure of urban thermal patterns.

## 3. Conceptual tensor structure

The model receives a temporal sequence of spatial observations and predicts a land surface temperature field.

The conceptual input structure is:

```
samples × time steps × rows × columns × variables
```

The conceptual output structure is:

```
samples × rows × columns × 1
```

The exact tensor order may vary according to the deep learning framework used, but the dimensional structure must be explicitly documented in the notebooks and source code.

## 4. Input variables

The explanatory variables are derived primarily from Landsat surface reflectance bands.

Expected spectral variables include:

* NDVI;
* NDMI or NDWI, according to the final manuscript terminology;
* NDBI;
* UI;
* SAVI;
* BSI, if retained in the final configuration.

The final input variable set must match the variables reported in the manuscript, the configuration files, and the executed notebooks.

Variables should not be included only because they improve apparent accuracy. Their inclusion must be justified by their physical, spectral, or climatic relationship with urban surface thermal behavior.

## 5. Climate forcing variables

The final model configuration may incorporate interannual climate forcing variables to contextualize the thermal response observed in the target year.

The retained climate forcings should represent changes in relevant atmospheric or surface-energy conditions, such as:

* interannual change in 2 m air temperature;
* interannual change in surface solar radiation;
* additional climate variables only if explicitly justified and documented.

These variables must not include observed LST from the target year or previous model predictions as input features.

The role of climate forcings is to provide contextual information about interannual thermal and radiative variability, not to replace the satellite-derived spatial predictors.

## 6. Target variable

The target variable is land surface temperature extracted from Landsat Collection 2 Level-2 products.

LST must be represented in physical units or transformed using a documented normalization procedure.

If the model is trained with normalized LST values, the inverse transformation must be applied before reporting physical error metrics such as RMSE and MAE in temperature units.

## 7. Model sequence

The modeling strategy compares a sequence of deep learning models with increasing structural complexity.

The expected sequence includes:

* U-Net 2D without explicit temporal memory;
* ConvLSTM U-Net;
* ConvLSTM + SE U-Net;
* final ConvLSTM + SE U-Net with interannual climate forcings.

This progressive comparison is intended to evaluate the contribution of:

* spatial encoder-decoder representation;
* explicit temporal memory;
* channel-wise recalibration;
* interannual climate context.

All models must be trained and evaluated using equivalent data partitions, normalization parameters, masks, and evaluation metrics.

## 8. Proposed deep learning architecture

The proposed architecture is based on a U-Net-like encoder-decoder structure extended with ConvLSTM blocks and channel attention mechanisms.

The architecture combines:

* convolutional encoding blocks;
* ConvLSTM blocks for temporal dependency modeling;
* U-Net-like decoding structure;
* skip connections between encoder and decoder levels;
* channel attention modules, such as squeeze-and-excitation blocks;
* interannual climate forcing variables in the final configuration.

The architecture is designed to estimate spatially continuous LST fields from multi-temporal spectral and climatic information.

## 9. U-Net component

The U-Net-like structure is used to preserve multi-scale spatial information.

The encoder extracts spatial features at progressively coarser levels, while the decoder reconstructs the prediction at the original spatial resolution.

Skip connections allow the model to recover local spatial detail that could be lost during downsampling.

This component is important because urban thermal patterns are spatially structured and strongly influenced by surface heterogeneity.

## 10. ConvLSTM component

ConvLSTM layers are used to model temporal dependencies while preserving spatial structure.

Unlike fully connected recurrent layers, ConvLSTM operations maintain spatial neighborhoods through convolutional gates.

This is relevant for remote sensing problems because the temporal evolution of urban thermal patterns is spatially structured rather than independent at the pixel level.

In this workflow, ConvLSTM blocks are used to process the antecedent sequence:

```
Y - 3, Y - 2, Y - 1
```

and support the prediction of:

```
LST(Y)
```

## 11. Channel attention component

Channel attention modules may be used to recalibrate feature maps according to their relative contribution to the prediction task.

Squeeze-and-excitation mechanisms are one possible implementation.

The inclusion of attention must be justified cautiously. Attention weights may indicate internal feature recalibration, but they are not equivalent to causal explanation or direct physical interpretability.

In this workflow, SE mechanisms are interpreted as channel-wise recalibration modules that allow the network to modulate internal spectral and climatic representations during learning.

## 12. Baseline and comparison strategy

Baseline and comparison models are required to determine whether the proposed deep learning model provides a meaningful methodological advantage.

The model comparison should include simpler or less complex alternatives, such as:

* U-Net 2D without temporal recurrence;
* ConvLSTM U-Net without SE attention;
* ConvLSTM + SE U-Net without climate forcings;
* the final ConvLSTM + SE U-Net with climate forcings.

Additional statistical or machine learning baselines may be included if they are implemented under comparable conditions.

A deep learning model should not be interpreted as superior unless it demonstrates consistent improvement over simpler alternatives under a controlled validation scheme.

## 13. Training strategy

The training strategy must report:

* training, validation, and test periods;
* number of scenes used;
* tensor dimensions;
* patch size;
* stride or overlap;
* batch size;
* number of epochs;
* optimizer;
* learning rate;
* loss function;
* early stopping criteria;
* hardware used for training.

All training decisions must be reproducible through the notebooks, source code, or configuration files.

## 14. Data partitioning

Data partitioning is critical in spatial-temporal modeling.

A naive random split of patches can inflate performance because neighboring patches are spatially autocorrelated and may share nearly identical information.

The preferred validation strategy should include temporal separation between training, validation, and test periods.

The partitioning strategy must be explicitly documented and consistently used across all models.

The final workflow must clearly identify:

* training years;
* validation years;
* test years;
* temporal sequence construction;
* target years;
* masking criteria;
* normalization parameters.

## 15. Leakage control

The modeling workflow must avoid information leakage between training, validation, and test datasets.

At minimum, the workflow must ensure that:

* normalization parameters are estimated using training data only;
* the target-year LST is not used as an input predictor;
* validation and test target years are not included in training;
* model comparison uses the same partitions and masks;
* climate forcings do not encode the observed LST target variable.

Leakage control is essential for producing defensible spatial-temporal prediction results.

## 16. Loss function

The loss function should be selected according to the regression objective.

Common options include:

* mean squared error;
* mean absolute error;
* Huber loss.

If training is performed on normalized LST values, final evaluation must still be reported in physical temperature units after inverse transformation.

Masked loss functions may be required when invalid pixels or water/non-study pixels are present in the raster domain.

## 17. Evaluation metrics

Model performance should be evaluated using statistical and spatial diagnostics.

Recommended metrics include:

* RMSE;
* MAE;
* coefficient of determination;
* bias;
* residual standard deviation;
* year-wise performance;
* spatial residual patterns.

Numerical metrics alone are insufficient. Spatial residual maps are required to evaluate whether the model systematically fails in specific urban, peri-urban, vegetated, coastal, or water-adjacent areas.

## 18. Model comparison

Model comparison must be performed under equivalent conditions.

All models should use:

* the same input data where applicable;
* the same training, validation, and test partitions;
* the same normalization parameters;
* comparable evaluation metrics;
* consistent masking criteria;
* comparable output domains.

A model with higher apparent accuracy but weaker validation design should not be interpreted as superior.

## 19. Prediction outputs

The trained model may be used to generate spatial LST prediction maps.

Expected outputs include:

* predicted LST maps;
* observed versus predicted maps;
* residual maps;
* error summary tables;
* annual or period-based diagnostics;
* hotspot or high-temperature recurrence maps.

Large prediction outputs are not stored in this repository.

## 20. Uncertainty and limitations

The modeling workflow should acknowledge the following limitations:

* Landsat temporal resolution limits the representation of short-term thermal dynamics.
* Cloud masking reduces the number of valid observations.
* Cross-sensor differences between Landsat 8 and Landsat 9 may introduce residual inconsistencies.
* LST is sensitive to acquisition time, surface moisture, atmospheric conditions, and land cover state.
* Climate forcings provide contextual information but do not fully represent local microclimatic processes.
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

The repository should allow reviewers to inspect the complete computational logic, even if full model training requires external datasets and high-performance computing resources.

## 22. Current status

This document defines the intended modeling structure for the repository.

The final version must be updated once the model architecture, hyperparameters, input variable set, climate forcing configuration, training partitions, and evaluation results are fixed in the executed notebooks and manuscript.

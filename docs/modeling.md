# Modeling

This document describes the modeling strategy used for spatial-temporal land surface temperature prediction in Barranquilla, Colombia.

## 1. Modeling objective

The modeling task is formulated as a supervised spatial-temporal regression problem.

The objective is to estimate land surface temperature from temporal sequences of Landsat-derived spectral variables. The model learns the relationship between previous or concurrent spectral conditions and the spatial distribution of surface temperature.

The target variable is:

* land surface temperature.

The input variables are Landsat-derived spectral indices and, if retained in the final configuration, additional geospatial predictors.

## 2. Conceptual formulation

The model receives a temporal sequence of spatial observations and predicts a land surface temperature field.

The conceptual input structure is:

```text
samples × time steps × rows × columns × variables
```

The conceptual output structure is:

```text
samples × rows × columns × 1
```

The exact tensor order may vary according to the deep learning framework used, but the dimensional structure must be explicitly documented in the notebooks and source code.

## 3. Input variables

The explanatory variables are derived from Landsat surface reflectance bands.

Expected input variables include:

* NDVI;
* NDWI;
* NDBI;
* UI;
* SAVI.

The final input variable set must match the variables reported in the manuscript, the configuration files, and the executed notebooks.

Variables should not be included only because they improve apparent accuracy. Their inclusion must be justified by their physical or spectral relationship with urban surface thermal behavior.

## 4. Target variable

The target variable is land surface temperature extracted from Landsat Collection 2 Level-2 products.

LST must be represented in physical units or transformed using a documented normalization procedure.

If the model is trained with normalized LST values, the inverse transformation must be applied before reporting physical error metrics such as RMSE and MAE in temperature units.

## 5. Baseline models

Baseline models are required to determine whether the proposed deep learning model provides a meaningful methodological advantage.

Recommended baselines include:

* persistence model;
* linear regression;
* random forest regression;
* convolutional neural network without temporal recurrence;
* ConvLSTM model without attention;
* U-Net model without temporal recurrence.

The selected baselines must be trained and evaluated using the same data partitions as the proposed model.

A deep learning model should not be interpreted as superior unless it demonstrates consistent improvement over simpler baselines under a controlled validation scheme.

## 6. Proposed deep learning architecture

The main model is based on a spatial-temporal deep learning architecture designed to preserve spatial structure while modeling temporal dependencies.

The proposed architecture may include:

* convolutional encoding blocks;
* ConvLSTM blocks for temporal dependency modeling;
* U-Net-like decoding structure;
* skip connections between encoder and decoder levels;
* channel attention modules, such as squeeze-and-excitation blocks.

The architecture is designed to estimate spatially continuous LST fields from multi-temporal spectral information.

## 7. ConvLSTM component

ConvLSTM layers are used to model temporal dependencies while preserving spatial structure.

Unlike fully connected recurrent layers, ConvLSTM operations maintain spatial neighborhoods through convolutional gates.

This is relevant for remote sensing problems because the temporal evolution of urban thermal patterns is spatially structured rather than independent at the pixel level.

## 8. U-Net component

The U-Net-like structure is used to preserve multi-scale spatial information.

The encoder extracts spatial features at progressively coarser levels, while the decoder reconstructs the prediction at the original spatial resolution.

Skip connections allow the model to recover local spatial detail that could be lost during downsampling.

## 9. Channel attention component

Channel attention modules may be used to recalibrate feature maps according to their relative contribution to the prediction task.

Squeeze-and-excitation mechanisms are one possible implementation.

The inclusion of attention must be justified cautiously. It should not be presented as interpretability by itself. Attention weights may indicate internal feature recalibration, but they are not equivalent to causal explanation.

## 10. Training strategy

The training strategy must report:

* training, validation, and test periods or spatial partitions;
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

## 11. Data partitioning

Data partitioning is critical in spatial-temporal modeling.

A naive random split of patches can inflate performance because neighboring patches are spatially autocorrelated and may share nearly identical information.

The preferred validation strategy should include at least one of the following:

* temporal holdout;
* spatial holdout;
* spatial block cross-validation;
* year-wise independent testing;
* combined spatial-temporal validation.

The partitioning strategy must be explicitly documented and consistently used across all models.

## 12. Loss function

The loss function should be selected according to the regression objective.

Common options include:

* mean squared error;
* mean absolute error;
* Huber loss.

If training is performed on normalized LST values, final evaluation must still be reported in physical temperature units after inverse transformation.

## 13. Evaluation metrics

Model performance should be evaluated using statistical and spatial diagnostics.

Recommended metrics include:

* RMSE;
* MAE;
* coefficient of determination;
* bias;
* residual standard deviation;
* year-wise performance;
* spatial residual patterns.

Numerical metrics alone are insufficient. Spatial residual maps are required to evaluate whether the model systematically fails in specific urban, peri-urban, vegetated, or water-adjacent areas.

## 14. Model comparison

Model comparison must be performed under equivalent conditions.

All models should use:

* the same input data;
* the same training, validation, and test partitions;
* the same normalization parameters;
* comparable evaluation metrics;
* consistent masking criteria.

A model with higher apparent accuracy but weaker validation design should not be interpreted as superior.

## 15. Prediction outputs

The trained model may be used to generate spatial LST prediction maps.

Expected outputs include:

* predicted LST maps;
* observed versus predicted maps;
* residual maps;
* error summary tables;
* annual or period-based diagnostics;
* hotspot or high-temperature recurrence maps.

Large prediction outputs are not stored in this repository.

## 16. Uncertainty and limitations

The modeling workflow should acknowledge the following limitations:

* Landsat temporal resolution limits the representation of short-term thermal dynamics.
* Cloud masking reduces the number of valid observations.
* Cross-sensor differences may introduce residual inconsistencies.
* LST is sensitive to acquisition time, surface moisture, atmospheric conditions, and land cover state.
* Deep learning models may reproduce spatial patterns without necessarily explaining their physical causes.
* Apparent accuracy can be inflated by spatial autocorrelation if validation is poorly designed.

These limitations must be considered when interpreting model performance and spatial prediction outputs.

## 17. Reproducibility requirements

The final modeling workflow must provide:

* executable notebooks;
* reusable source code;
* configuration files;
* model parameter documentation;
* data partitioning metadata;
* environment specification;
* clear instructions for reproducing evaluation tables and figures.

The repository should allow reviewers to inspect the complete computational logic, even if full model training requires external datasets and high-performance computing resources.

## 18. Current status

This document defines the intended modeling structure. It must be updated once the final notebooks, model architecture, hyperparameters, and evaluation results are fixed.

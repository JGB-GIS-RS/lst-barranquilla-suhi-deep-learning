# Methodological workflow

This document describes the computational workflow implemented for modeling surface urban heat patterns in Barranquilla, Colombia, using Landsat-derived land surface temperature, spectral indices, interannual climate forcings, and deep learning models.

## 1. Landsat 8/9 scene inventory

The workflow begins with the identification and organization of Landsat 8/9 Collection 2 Level-2 scenes covering the Barranquilla study area.

The operational period is:

```
2013–2025
```

The year 2026 is excluded from the operational analysis because the annual period is incomplete.

The Landsat WRS-2 reference used for the scene inventory is:

```
Path: 9
Row: 52
```

The inventory includes:

* satellite platform and sensor;
* acquisition date;
* year, month, and day of year;
* path and row;
* cloud cover metadata;
* processing level;
* collection category;
* scene identifier;
* temporal grouping for modeling.

The scene inventory is documented in:

```
notebooks/01_scene_inventory_landsat_8_9.ipynb
```

Lightweight inventory tables are stored in:

```
data/scene_inventory/
```

These tables allow users and reviewers to inspect the temporal availability of the input scenes without downloading large raster datasets.

## 2. Data sources

The workflow uses Landsat 8/9 Collection 2 Level-2 products as the primary satellite data source.

The corresponding Earth Engine collections are:

```
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

The workflow also allows the incorporation of interannual climate forcing variables in the final model configuration.

These climate forcings are used to provide contextual information about interannual thermal and radiative variability. They must not include observed LST from the target year or previous model predictions as input features.

## 3. Preprocessing

Preprocessing includes the preparation of Landsat-derived variables before temporal dataset construction.

The main operations are:

* filtering by acquisition date and study area;
* applying quality masking using Landsat Collection 2 Level-2 QA bands;
* removing clouds, cloud shadows, cirrus contamination, fill values, and invalid pixels;
* extracting land surface temperature from Level-2 products;
* computing spectral indices from surface reflectance bands;
* harmonizing spatial resolution and grid alignment;
* clipping all variables to the study area;
* generating valid-pixel masks and quality-control summaries.

The preprocessing stage produces spatially aligned raster layers suitable for temporal stacking, tensor construction, and patch extraction.

Large preprocessed raster outputs are not stored in this GitHub repository.

## 4. Derived variables

The primary target variable is land surface temperature.

The explanatory variables are spectral indices derived from Landsat surface reflectance bands. These indices are used to represent vegetation condition, moisture, built-up surfaces, bare soil response, and urban spectral behavior.

Expected spectral variables include:

* NDVI;
* NDMI or NDWI, according to the final manuscript terminology;
* NDBI;
* UI;
* SAVI;
* BSI, if retained in the final configuration.

The final model configuration may also include interannual climate forcing variables, such as:

* interannual change in 2 m air temperature;
* interannual change in surface solar radiation;
* additional climate variables only if explicitly justified and documented.

Only variables used consistently across the temporal sequence are included in the final modeling dataset.

## 5. Normalization

Normalization is applied to ensure numerical stability during model training.

The procedure distinguishes between:

* spectral indices, which may be scaled using robust min-max normalization;
* LST, which may be standardized using training-period statistics;
* climate forcings, which should also be normalized using training-period statistics when included.

Normalization parameters must be estimated using training data only.

Validation and test data must not be used to estimate normalization parameters. This is required to avoid information leakage.

## 6. Temporal dataset construction

Preprocessed raster layers are organized into multi-temporal datasets.

The modeling framework uses three antecedent temporal states to predict the land surface temperature of a target year.

The temporal formulation is:

```
T1 = Y - 3
T2 = Y - 2
T3 = Y - 1
Target = LST(Y)
```

The conceptual input structure is:

```
samples × time steps × rows × columns × variables
```

The conceptual output structure is:

```
samples × rows × columns × 1
```

The exact tensor order must be documented in the corresponding notebooks and source-code implementation.

## 7. Patch extraction

Spatial-temporal patches are extracted from the aligned raster datasets to train and evaluate the models.

Patch extraction must preserve:

* temporal consistency;
* spatial alignment;
* valid-pixel masks;
* target-year association;
* input-output pairing between antecedent variables and target LST.

The patch extraction procedure must avoid introducing leakage between training, validation, and test target years.

Random patch-level splitting should be avoided unless its limitations are explicitly acknowledged and controlled.

## 8. Data partitioning

The workflow uses a temporal holdout strategy.

The final implementation must explicitly document:

* training years;
* validation years;
* test years;
* target years;
* antecedent years used for each target year;
* masking criteria;
* normalization parameters.

All models must use the same partitioning strategy, normalization parameters, and masking logic to allow fair comparison.

## 9. Model development

The modeling task is formulated as supervised spatial-temporal regression.

The target variable is:

```
LST(Y)
```

The proposed modeling framework is based on a U-Net-like encoder-decoder architecture extended with ConvLSTM blocks and channel attention mechanisms.

The model sequence is organized progressively:

* U-Net 2D without explicit temporal memory;
* ConvLSTM U-Net;
* ConvLSTM + SE U-Net;
* final ConvLSTM + SE U-Net with interannual climate forcings.

This progressive sequence allows evaluation of the contribution of:

* spatial encoder-decoder representation;
* explicit temporal memory;
* channel-wise recalibration;
* interannual climate context.

## 10. Leakage control

The workflow must explicitly prevent information leakage.

At minimum, the workflow must ensure that:

* target-year LST is not used as an input predictor;
* previous model predictions are not used as input variables;
* validation and test target years are not included in training;
* normalization parameters are estimated using training data only;
* all model comparisons use equivalent data partitions;
* climate forcings do not encode observed target-year LST.

Leakage control is essential for producing defensible spatial-temporal prediction results.

## 11. Model training

The training procedure must document:

* model architecture;
* input variables;
* temporal sequence;
* patch size;
* batch size;
* number of epochs;
* optimizer;
* learning rate;
* loss function;
* early stopping criteria;
* hardware or runtime environment.

Masked loss functions may be required when invalid pixels or non-study pixels are present in the raster domain.

## 12. Model evaluation

Model performance is evaluated using statistical and spatial diagnostics.

Expected metrics include:

* RMSE;
* MAE;
* coefficient of determination;
* bias;
* residual standard deviation;
* year-wise performance.

Spatial diagnostics should include:

* observed versus predicted maps;
* residual maps;
* year-wise error maps;
* spatial error patterns;
* hotspot or high-temperature recurrence diagnostics, if retained in the final analysis.

Numerical metrics alone are insufficient. Spatial residual analysis is required to identify systematic overestimation or underestimation in urban, peri-urban, vegetated, coastal, or water-adjacent areas.

## 13. Prediction outputs

The trained models may be used to generate spatial LST prediction maps.

Expected outputs include:

* predicted LST maps;
* observed versus predicted maps;
* residual maps;
* error summary tables;
* annual or period-based diagnostics;
* hotspot or high-temperature recurrence maps.

Large prediction outputs are not stored in this repository.

Only lightweight tables or figures may be included when they improve methodological transparency and do not exceed repository size constraints.

## 14. Repository role

This repository is intended to support transparency, reproducibility, and methodological traceability.

It includes:

* documentation;
* configuration files;
* executable notebooks;
* reusable source-code structure;
* environment files;
* lightweight inventory tables.

It does not include:

* original Landsat scenes;
* full-resolution raster stacks;
* tensors;
* patch datasets;
* trained model checkpoints;
* large prediction maps.

Full workflow reproduction requires access to the original data sources, external storage for large datasets, and adequate computational resources.

## 15. Current status

The repository is under active development.

At this stage, the workflow documentation, configuration files, scene inventory notebook, and Landsat 8/9 scene inventory tables are included.

Final preprocessing notebooks, tensor construction scripts, model training notebooks, evaluation scripts, and output-generation workflows will be added as the manuscript workflow is consolidated.

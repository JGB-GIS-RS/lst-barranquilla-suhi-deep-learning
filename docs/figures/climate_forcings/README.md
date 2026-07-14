# Climate-radiative diagnostic figures

This directory contains lightweight diagnostic figures generated during the
NASA POWER data-processing and descriptor-selection workflow.

The figures summarize annual regional climate-radiative conditions, interannual
changes, and the two descriptors retained for the final M5 configuration.

## Included figures

```text
05_annual_precip_total_2013_2025.png
05_annual_solar_radiation_mean_2013_2025.png
05_annual_t2m_mean_2013_2025.png
05_interannual_delta_solar_radiation_2014_2025.png
05_interannual_delta_t2m_2014_2025.png
05_selected_climate_inputs_m5b_2016_2025.png
```

These figures document:

- annual total precipitation;
- annual mean surface solar radiation;
- annual mean 2 m air temperature;
- interannual change in mean surface solar radiation;
- interannual change in mean 2 m air temperature;
- the two regional descriptors retained as auxiliary inputs for M5.

The retained M5 descriptors are:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

## Interpretation

The NASA POWER variables are annual regional descriptors associated with the
centroid of the modeling domain. They are not spatially distributed climate
fields at Landsat resolution.

The figure filename containing `m5b` is retained as a historical internal
identifier from the development workflow. The public manuscript and repository
label for the final model is:

```text
M5: T3-Climate ConvLSTM-SE U-Net
```

These diagnostic figures support methodological traceability and do not by
themselves establish causal relationships between climate variables and LST
prediction errors.

## Storage scope

This directory is limited to lightweight diagnostic PNG files.

The following products are intentionally excluded:

- full-resolution climate rasters;
- Landsat-derived raster products;
- tensors and patch datasets;
- model checkpoints;
- prediction and residual maps;
- large intermediate outputs.

The corresponding processing logic is documented in:

```text
notebooks/05_climate_forcing_integration.ipynb
docs/data_sources.md
docs/modeling.md
docs/workflow.md
```

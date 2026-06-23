# Climate forcings

This directory stores lightweight tabular products derived from the NASA POWER climate forcing workflow.

## Purpose

The climate forcing workflow builds annual regional descriptors for the Barranquilla model domain using daily NASA POWER data. These variables are used to evaluate and document interannual atmospheric context for the LST/SUHI modeling workflow.

NASA POWER variables are not used as pixel-level spatial raster predictors. They are annual descriptors associated with the model-domain centroid.

## Temporal scope

```text
2013-2025
```

The year 2026 is excluded from the operational annual workflow because the annual period is incomplete.

## Candidate NASA POWER variables

The workflow may evaluate daily variables such as:

- `T2M`: 2 m air temperature;
- `T2M_MAX`: maximum 2 m air temperature;
- `T2M_MIN`: minimum 2 m air temperature;
- `ALLSKY_SFC_SW_DWN`: all-sky surface shortwave downward radiation;
- `PRECTOTCORR`: corrected precipitation;
- `RH2M`: 2 m relative humidity;
- `WS2M`: 2 m wind speed.

## Final retained variables

The final climate-enhanced model retains only two interannual descriptors:

- `delta_t2m_mean_Y_minus_Yminus1`;
- `delta_solar_radiation_mean_Y_minus_Yminus1`.

These variables represent the change between the target year `Y` and the immediately preceding year `Y-1`.

## Expected lightweight outputs

Expected files may include:

- `05_nasa_power_requested_variables_2013_2025.csv`
- `05_nasa_power_daily_availability_2013_2025.csv`
- `05_annual_climate_forcing_nasa_power_2013_2025.csv`
- `05_annual_climate_forcing_anomalies_2013_2025.csv`
- `05_annual_climate_forcing_deltas_2014_2025.csv`
- `05_model_climate_inputs_selected_m5b_2016_2025.csv`
- `05_climate_variable_selection_trace_2016_2025.csv`
- `05_climate_forcing_integration_summary_2013_2025.json`

Only lightweight CSV and JSON files should be stored here.

## Related notebook

```text
notebooks/05_climate_forcing_integration.ipynb
```

## Data policy

Do not store large raster, tensor, patch, checkpoint, or prediction-map products in this directory.

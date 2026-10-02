# SUHI diagnostic tables

This directory contains lightweight tabular outputs exported by the finalized SUHI/reference workflow implemented in:

- `notebooks/10_emc_built_2022_reference_and_suhi_diagnostics.ipynb`
- `notebooks/11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb`

They are included so that the principal numerical claims reported in the manuscript can be inspected without requiring the full external GeoTIFF archive.

## Files

- `reference_stats.csv` — annual observed/predicted non-urban reference temperature and reference error.
- `reference_support.csv` — fixed reference geometry and annual common-valid support.
- `sensitivity_R.csv` — reference geometry and observed `Tref` under alternative `A_min` values.
- `sensitivity_suhi_Amin_025_0333_050.csv` — full SUHI sensitivity analysis for `A_min = 0.25, 0.333, 0.50 km²`.
- `urban_support_comparison_BUall_vs_BUcore.csv` — comparison of complete built-up support and consolidated-core support.
- `suhi_p95_domain_vs_urban.csv` — P95 comparison for the full common domain and the final urban support.
- `suhi_error_decomposition_2023_2025.csv` — decomposition of urban-reference contrast error.
- `suhi_summary_final.csv` — final annual SUHI summary used for the reported 2023–2025 diagnostics.
- `thermal_gradient.csv` — median-temperature differences between successive peripheral bands.
- `thermal_stats.csv` — annual thermal statistics by distance band.
- `spectral_stats.csv` — NDVI, NDBI and NDMI statistics used as spectral controls for the peripheral reference assessment.

## Frozen reference definition

```text
BU_all  = built-up fraction >= 0.10
BU_core = 8-neighbor connected components >= 0.333 km²
R       = 4–6 km from BU_core, within fixed TEST support, excluding BU_all
```

The fixed reference geometry contains 167,527 pixels (150.7743 km²). Annual `Tref` values are calculated on the subset of that geometry where observed and predicted LST are simultaneously valid, so the annual valid-pixel counts are slightly smaller than the fixed geometric support.

These CSV files are lightweight derived outputs. Complete regeneration still requires the external full-resolution rasters documented in `docs/reproducibility_notes.md`.

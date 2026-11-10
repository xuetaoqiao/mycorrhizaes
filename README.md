# Data and code: Mixed mycorrhizae and ectomycorrhizal dominance buffer forests against extreme drought

This package holds the analysis dataset and the R code that reproduce the numerical results of every figure and table in the paper:

- **Main text:** Figures 2 to 5.
- **Supporting Information:** Figs S1 to S8 and Tables S1 to S7.

Figure 1 is a conceptual diagram and has no data.

## Contents

| Folder or file | What it holds |
|:---|:---|
| `data/paper2_drought_events_anonymized.csv.gz` | The analysis dataset. One row per drought event, that is, one forest plot in one extreme-drought year: 363,085 events in 83,092 plots of the U.S. Forest Inventory and Analysis (FIA) program, 2000 to 2025. |
| `data/data_dictionary.csv` | Definition, type and use of each of the 110 columns. |
| `code/main_text/` | Scripts for Figures 2 to 5. |
| `code/supporting_information/` | Scripts for Figs S1 to S8 and Tables S1 to S7. |
| `code/00_config.R`, `code/run_all.R` | Shared settings; runs every script in order. |
| `output/` | The CSV results that the code writes, for checking. Running the code rewrites this folder. |

Notes on the dataset:

- **Plot codes:** plot identifiers are random codes (P00001 to P83092). They link the events of the same plot, which the models need for plot random effects and plot-clustered standard errors.
- **No coordinates:** plot coordinates are not included.
- **Processing layer:** the dataset is the layer of processed data from which all results are computed, not the raw inventory, satellite and climate data. Its variables are:
  - drought resistance from MODIS NDVI;
  - tree species richness;
  - the basal-area percentage of ectomycorrhizal trees;
  - host mean pairwise phylogenetic distance (MPD);
  - covariates;
  - basal-area shares of the 80 most common tree species.

The code computes every estimate, test and table and writes it as a CSV file. It does not draw the figures.

## How to run

Software: R (tested with R 4.5.2) with these packages:

- `data.table`, `lme4`, `lmerTest`, `emmeans`, `car`, `metafor`, `MASS`, `digest`, `jsonlite`;
- `splines` and `parallel`, which come with R.

```
Rscript code/run_all.R          # everything, about 5 minutes with 11 cores
Rscript code/run_all.R main     # main text only
Rscript code/run_all.R si       # Supporting Information only (needs the main-text results)
```

Each script can also be run on its own, for example `Rscript code/main_text/Fig5_results.R`. The Supporting Information scripts read the main-text results, so run the main-text scripts first.

## Which script gives which result

| Paper item | Script | Main output files (in `output/`) |
|:---|:---|:---|
| Fig. 2a | `main_text/Fig2_1_results.R` | `main_text/Fig2/panel_a_EcM_bin_summary.csv` (1% EcM classes); `panel_a_coefficients.csv`, `panel_a_covariance.csv`, `panel_a_quadratic_curve.csv` (quadratic mixed model) |
| Fig. 2b, 2c | `main_text/Fig2_1_results.R` | `panels_bc_lollipop_values.csv` (EcM percentage at maximum resistance); `richness_sample_support.csv`; `panel_c_MPD_classes.csv` |
| Fig. 2b, covariates added (Table S2) | `main_text/Fig2_2_environment_adjusted_results.R` | `main_text/Fig2/panel_b_environment_adjusted.csv` |
| Fig. 3 | `main_text/Fig3_results.R` | `main_text/Fig3/Fig3_results_by_ecoregion.csv`, `Fig3_paired_test.csv` |
| Fig. 4a | `main_text/Fig4_1_panel_a_results.R` | `main_text/Fig4/tables/panel_a_regression.csv`, `panel_a_prediction.csv` |
| Fig. 4b, 4c | `main_text/Fig4_2_panels_b_c_results.R` | `panel_b_relative_effects.csv`, `panel_c_standardized_coefficients.csv`, `*_VIF.csv`, `model_diagnostics.csv` |
| Fig. 5a | `main_text/Fig5_results.R` | `main_text/Fig5/tables/control_walk.csv`, `control_walk_change.csv` |
| Fig. 5b | `main_text/Fig5_results.R` | `mpd_slope_within_class.csv` |
| Fig. 5c | `main_text/Fig5_results.R` | `levelwise_richness.csv`, `slopes_richness.csv` |
| Fig. 5d | `main_text/Fig5_results.R` | `within_plot_slopes.csv`, `within_plot_interaction.csv` |
| Figs S1 to S3 | `supporting_information/FigS1-S3_results.R` | `supporting_information/figures_results/FigS1_*.csv` to `FigS3_*.csv` |
| Figs S4, S5 | `supporting_information/FigS4-S5_results.R` | `FigS4_monospecific_AM_vs_EcM_statistics.csv`, `FigS5_resistance_and_richness_statistics.csv` |
| Figs S6 to S8 | `supporting_information/FigS6-S8_results.R` | the rows of the Figure 5 tables shown in each figure |
| Tables S1 to S7 | `supporting_information/Tables_S1-S7.R` | `supporting_information/tables/Table_S1_*.csv` to `Table_S7_*.csv` |

`main_text/Fig5_results.R` also writes every other Figure 5 analysis named in the paper to `output/main_text/Fig5/tables/`:

- the alternative type definitions;
- the sample and covariate variants;
- the ecoregion-level meta-analysis;
- the host MPD controls;
- two-species stands with identical MPD.

## Methods in brief

- **Figure 2:** linear mixed models with plot random intercepts (`lme4`, maximum likelihood). Confidence intervals of the EcM percentage at maximum resistance use the Fieller method.
- **Figure 4:** linear mixed models with ecoregion, plot and year random intercepts. Responses and predictors are standardized.
- **Figure 5:** least squares with ecoregion × year fixed effects (and richness fixed effects where stated), with standard errors clustered by plot. Fixed effects are removed by alternating projections (`Fig5_lib.R`).
- **Figure 3:** exact Wilcoxon signed-rank test across ecoregions.
- **Fig. S4:** linear mixed model with estimated marginal means (`emmeans`).

## Notes

- **Maps:** the maps in Figs S1a and S5a need plot coordinates, which are not part of this dataset. For Fig. S5a the code gives the plot-level means that the map shows.
- **Stored precision:** numbers are stored in their shortest exact decimal form, with up to 17 significant digits. Some CSV readers round the last binary digit (about 1e-16 relative), which does not change any reported result.
- **Plot codes and results:** with the anonymized plot codes, the mixed-model estimates can differ from the published values in the 6th or 7th significant digit, because the optimizer runs on a different ordering of plots. All reported values are unchanged.
- **Raw data sources:** see the Data availability statement of the paper (FIA, MODIS MOD13Q1 NDVI, climate and drought data, USFS ecological provinces).

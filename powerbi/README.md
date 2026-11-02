# Power BI Dashboard — Build Guide

This folder contains everything needed to build an interactive Power BI companion to the
Jupyter notebook analysis: a ready-to-import dataset and a step-by-step spec for the report
pages, relationships, and DAX measures.

> **Why a separate dataset instead of the raw `city_day.csv`?** The raw CPCB file isn't
> committed to this repo (29,531-row dataset, licensed separately on Kaggle — see
> [`../data/README.md`](../data/README.md)). The CSVs in [`dataset/`](dataset) are
> pre-aggregated extracts of the notebook's **own verified outputs** (`tables/*.csv` and the
> published numbers in [`../reports/AQI_Analysis_Report.md`](../reports/AQI_Analysis_Report.md)),
> so the dashboard stays consistent with the report without needing the full raw file. If you
> later add `data/city_day.csv` locally, you can rebuild these extracts at full row-level
> granularity by re-running the notebook with extra `to_csv()` export lines — see
> [Extending to row-level data](#extending-to-row-level-data) below.

## 1. Dataset files

| File | Grain | Source | Notes |
|------|-------|--------|-------|
| `dim_city_stats.csv` | 1 row / city (26) | `tables/table_03_city_stats.csv` | Adds `Region` (geographic grouping) and `Latitude`/`Longitude` (public city-centre coordinates) for map visuals — not part of the original notebook output. |
| `fact_yearly_aqi.csv` | city × year (8 focus cities) | `tables/table_04_yearly_aqi.csv`, unpivoted | Long format, ready for a line chart with a City legend. |
| `fact_bucket_distribution.csv` | 1 row / AQI category (6) | `tables/table_05_bucket_distribution.csv` | Adds `SortOrder` (1–6) so Power BI sorts Good→Severe instead of alphabetically, and an `Unhealthy` flag. |
| `fact_covid_comparison.csv` | 1 row / city (26) | `tables/table_06_covid_comparison.csv` | **Recomputed** `Change_Pct` as `(Lockdown − Pre-Lockdown) / Pre-Lockdown × 100` so a negative number always means "AQI improved during lockdown" — the original notebook column used the opposite ratio and sign convention, which read backwards next to the report's own narrative. Verified against all 26 rows. |
| `fact_unhealthy_summary.csv` | 1 row / major city (8) | `tables/table_07_unhealthy_summary.csv` | Direct reuse, renamed columns. |
| `fact_seasonal_avg.csv` | 1 row / season (4) | Report §5.4 | Published rounded values (`~221`, etc.) — not re-derivable from the CSV tables alone, so treat as indicative, not exact. |
| `fact_dayofweek.csv` | 1 row / weekday (7) | Report §5.5 ("actual notebook output" table) | Exact values. |
| `fact_pollutant_correlation.csv` | 1 row / pollutant (6) | Report §6.1 | Pearson r with AQI, exact values. |
| `fact_missing_values.csv` | 1 row / raw column (16) | `tables/table_01_missing_values.csv` | For a data-quality page. |
| `fact_pollutant_stats.csv` | 1 row / pollutant+AQI (8) | `tables/table_02_summary_stats.csv` | Descriptive stats (mean/std/percentiles) for distribution cards. |

All values trace back to this project's own notebook outputs or report tables — nothing here
is sourced from outside the project.

## 2. Build steps (Power BI Desktop)

1. **Get data → Folder** (or **Text/CSV** ten times) → point at `powerbi/dataset/` → **Combine & Transform** isn't needed since these are separate tables, not one schema; load each CSV individually as its own table (uncheck "Combine files").
2. In **Power Query Editor**, confirm data types: `Year` → Whole Number, all `*_AQI*` / `*_Pct*` / `Latitude` / `Longitude` → Decimal Number, `SortOrder` → Whole Number. Click **Close & Apply**.
3. **Model view** — create these relationships (all one-to-many, single direction, from `dim_city_stats[City]`):
   - `dim_city_stats[City]` → `fact_yearly_aqi[City]`
   - `dim_city_stats[City]` → `fact_covid_comparison[City]`
   - `dim_city_stats[City]` → `fact_unhealthy_summary[City]`
   - The remaining fact tables (`fact_bucket_distribution`, `fact_seasonal_avg`, `fact_dayofweek`, `fact_pollutant_correlation`, `fact_missing_values`, `fact_pollutant_stats`) are global aggregates (all 26 cities combined) and have **no city key** — leave them unrelated; they feed their own standalone visuals/pages.
4. Add the DAX measures from §3 below (**Modeling → New Measure**, on `dim_city_stats`).
5. Build the four pages described in §4.
6. **File → Save As** → `AQI_Dashboard.pbix` in this same `powerbi/` folder (the `.pbix` binary itself isn't committed to Git by default — see [`.gitignore`](../.gitignore) — commit it manually if you want it versioned, or publish to the Power BI Service and share that link in the README instead).

## 3. Suggested DAX measures

```dax
Avg City AQI          = AVERAGE(dim_city_stats[Mean_AQI])
Unhealthy City Count   = CALCULATE(COUNTROWS(dim_city_stats), dim_city_stats[Unhealthy_Pct] > 20)
Total Unhealthy Days % = DIVIDE(SUM(fact_bucket_distribution[Count]), 24850, 0) * 100  -- sanity check vs Table 05 total (should read 100%)
Pct Unhealthy (filtered) =
    DIVIDE(
        CALCULATE(SUM(fact_bucket_distribution[Count]), fact_bucket_distribution[Unhealthy] = "Yes"),
        SUM(fact_bucket_distribution[Count])
    )
Lockdown AQI Change (avg) = AVERAGE(fact_covid_comparison[Change_Pct])
City Rank by Unhealthy % = RANKX(ALL(dim_city_stats), dim_city_stats[Unhealthy_Pct], , DESC)
```

## 4. Suggested report pages

| Page | Visuals |
|------|---------|
| **1. Overview** | Card — `Avg City AQI`; Card — `Unhealthy City Count`; Map (bubble, size = `Mean_AQI`, lat/long from `dim_city_stats`); Donut — `fact_bucket_distribution` (Category, Count, sorted by `SortOrder`), colour-mapped Good→Severe (green→dark red) |
| **2. Temporal (RQ1)** | Line chart — `fact_yearly_aqi` (Year on X, `Mean_AQI_Capped` on Y, legend = City); Bar — `fact_seasonal_avg` (Season on X, sorted by `SortOrder`); Bar with error context — `fact_dayofweek` (Day on X, sorted by `SortOrder`); Clustered bar — `fact_covid_comparison` (City on Y, `Pre_Lockdown_AQI` vs `Lockdown_AQI`), sorted by `Change_Pct` |
| **3. Pollutants (RQ2)** | Bar chart — `fact_pollutant_correlation` (Pollutant on X, `Correlation_with_AQI` on Y, colour by `Strength`); Table — `fact_pollutant_stats` (Mean/Std/Min/Max per pollutant) |
| **4. City Exposure (RQ3)** | Bar — `fact_unhealthy_summary` (City on Y, `Unhealthy_Pct` on X, sorted descending); Table/matrix — `dim_city_stats` with a slicer on `Region`; Scatter — `Mean_AQI` vs `Unhealthy_Pct` from `dim_city_stats`, bubble size = `Days`, coloured by `Region` |

Add a **Region** slicer and a **City** slicer (synced across pages 2–4) so a viewer can isolate
the north–south divide the report describes.

## 5. Extending to row-level data

To rebuild these extracts (or go further — e.g. a date-level drill-through) from the full
29,531-row dataset:

1. Download `city_day.csv` from [Kaggle](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india) into `data/` (see [`../data/README.md`](../data/README.md)).
2. In `notebook/air-quality-index-aqi-analysis.ipynb`, after the cleaning cell (`df` is ready),
   add: `df.to_csv("powerbi/dataset/fact_city_day_full.csv", index=False)`.
3. Re-run the notebook, then import that CSV into Power BI as an additional fact table related
   to `dim_city_stats[City]` via its `City` column — this unlocks city×date-level drill-through
   that the pre-aggregated extracts above can't provide.

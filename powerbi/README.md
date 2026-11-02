# Power BI Dashboard

This folder contains a working **Power BI Project (PBIP)** — `AQI_Dashboard.pbip` — plus the
dataset it loads from. Double-click `AQI_Dashboard.pbip` to open it in Power BI Desktop: the
data model (10 tables, 3 relationships, 6 DAX measures) loads automatically, and four named
report pages (Overview · Temporal RQ1 · Pollutants RQ2 · City Exposure RQ3) are ready for you
to drop visuals onto.

> **Why a project file instead of a `.pbix`?** PBIP is Power BI's official, git-friendly
> open format — the semantic model is plain-text TMDL and the report is plain-text JSON (PBIR),
> both documented by Microsoft and designed to be authored outside the Power BI Desktop UI. That's
> what makes it possible to generate the tables, relationships, and measures below programmatically
> and have Power BI Desktop open them directly, instead of only being able to hand you a
> build-it-yourself guide. See [Project structure](#2-project-structure) for what's in each file.

## 1. What's already built vs. what's left

| Done for you | Still needs you (in Power BI Desktop) |
|---|---|
| All 10 tables loaded from `dataset/*.csv` via Power Query | Drag fields onto the 4 blank pages to create the actual charts |
| 3 relationships wired (`dim_city_stats[City]` → the 3 city-keyed fact tables) | Pick chart types / formatting (see §5 for the suggested spec) |
| 6 DAX measures created in a `_Measures` table | Add slicers (Region, City) and sync them across pages |
| 4 report pages created and named | Save (`Ctrl+S`) once you're happy — this writes back into these same files |

Hand-authoring full chart visuals (exact field-well bindings, axis config) as raw JSON without
being able to see the rendered result wasn't reliable enough to also script — so the pages are
real but intentionally blank. Everything that *is* scripted (the model, relationships, measures)
was validated by actually opening the project in Power BI Desktop (version 2.159.883.0) and
confirming it loads without errors before this was committed.

> **Why a separate dataset instead of the raw `city_day.csv`?** The raw CPCB file isn't
> committed to this repo (29,531-row dataset, licensed separately on Kaggle — see
> [`../data/README.md`](../data/README.md)). The CSVs in [`dataset/`](dataset) are
> pre-aggregated extracts of the notebook's **own verified outputs** (`tables/*.csv` and the
> published numbers in [`../reports/AQI_Analysis_Report.md`](../reports/AQI_Analysis_Report.md)),
> so the dashboard stays consistent with the report without needing the full raw file. If you
> later add `data/city_day.csv` locally, you can rebuild these extracts at full row-level
> granularity by re-running the notebook with extra `to_csv()` export lines — see
> [Extending to row-level data](#extending-to-row-level-data) below.

## 2. Project structure

```
powerbi/
├── AQI_Dashboard.pbip                  # open this in Power BI Desktop
├── AQI_Dashboard.Report/               # PBIR report definition (4 pages, plain-text JSON)
├── AQI_Dashboard.SemanticModel/        # TMDL semantic model (tables, relationships, measures)
├── dataset/                            # the 10 CSVs the model loads from
└── README.md                           # this file
```

### Dataset files

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

## 3. Open it

1. Double-click `AQI_Dashboard.pbip`. Power BI Desktop opens with the title bar reading
   "AQI_Dashboard" and the model already loaded (give it a few seconds to import the 10 CSVs on
   first open).
2. If a CSV path warning appears: the tables load via `File.Contents("D:\Project\Air Quality
   Index (AQI) Analysis\air-quality-index-project\powerbi\dataset\...")` — an absolute path, since
   that's portable for *this* machine but not if you move/clone the folder elsewhere. To repoint
   it: **Transform data** → select each query → fix the path in the formula bar of its `Source`
   step → **Close & Apply**.
3. You'll land on the (currently blank) **Overview** page with 3 other pages in the pane on the
   left. Build the visuals per §5 below, then save (`Ctrl+S`) — this writes straight back into
   `AQI_Dashboard.Report/` and `AQI_Dashboard.SemanticModel/`, so `git diff` stays readable.
4. Optional: **File → Save As → Power BI file (.pbix)** if you also want a single binary file to
   share with someone who doesn't need the source project (not committed by default — see
   [`.gitignore`](../.gitignore)).

## 4. DAX measures (already created, in the `_Measures` table)

```dax
'Avg City AQI'                = AVERAGE(dim_city_stats[Mean_AQI])
'Unhealthy City Count'         = CALCULATE(COUNTROWS(dim_city_stats), dim_city_stats[Unhealthy_Pct] > 20)
'Total Bucket Days'            = SUM(fact_bucket_distribution[Count])
'Pct Unhealthy Days' =
    DIVIDE(
        CALCULATE(SUM(fact_bucket_distribution[Count]), fact_bucket_distribution[Unhealthy] = "Yes"),
        SUM(fact_bucket_distribution[Count])
    )
'Avg Lockdown AQI Change'      = AVERAGE(fact_covid_comparison[Change_Pct])
'City Rank by Unhealthy Pct'   = RANKX(ALL(dim_city_stats), dim_city_stats[Unhealthy_Pct], , DESC)
```

Find them in the Fields pane under the `_Measures` table — drag straight onto any visual.

## 5. Suggested report pages (what to build on each blank page)

| Page | Visuals |
|------|---------|
| **1. Overview** | Card — `Avg City AQI`; Card — `Unhealthy City Count`; Map (bubble, size = `Mean_AQI`, lat/long from `dim_city_stats`); Donut — `fact_bucket_distribution` (Category, Count, sorted by `SortOrder`), colour-mapped Good→Severe (green→dark red) |
| **2. Temporal (RQ1)** | Line chart — `fact_yearly_aqi` (Year on X, `Mean_AQI_Capped` on Y, legend = City); Bar — `fact_seasonal_avg` (Season on X, sorted by `SortOrder`); Bar with error context — `fact_dayofweek` (Day on X, sorted by `SortOrder`); Clustered bar — `fact_covid_comparison` (City on Y, `Pre_Lockdown_AQI` vs `Lockdown_AQI`), sorted by `Change_Pct` |
| **3. Pollutants (RQ2)** | Bar chart — `fact_pollutant_correlation` (Pollutant on X, `Correlation_with_AQI` on Y, colour by `Strength`); Table — `fact_pollutant_stats` (Mean/Std/Min/Max per pollutant) |
| **4. City Exposure (RQ3)** | Bar — `fact_unhealthy_summary` (City on Y, `Unhealthy_Pct` on X, sorted descending); Table/matrix — `dim_city_stats` with a slicer on `Region`; Scatter — `Mean_AQI` vs `Unhealthy_Pct` from `dim_city_stats`, bubble size = `Days`, coloured by `Region` |

Add a **Region** slicer and a **City** slicer (synced across pages 2–4) so a viewer can isolate
the north–south divide the report describes.

## 6. Extending to row-level data

To rebuild these extracts (or go further — e.g. a date-level drill-through) from the full
29,531-row dataset:

1. Download `city_day.csv` from [Kaggle](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india) into `data/` (see [`../data/README.md`](../data/README.md)).
2. In `notebook/air-quality-index-aqi-analysis.ipynb`, after the cleaning cell (`df` is ready),
   add: `df.to_csv("powerbi/dataset/fact_city_day_full.csv", index=False)`.
3. Re-run the notebook, then import that CSV into Power BI as an additional fact table related
   to `dim_city_stats[City]` via its `City` column — this unlocks city×date-level drill-through
   that the pre-aggregated extracts above can't provide.

<div align="center">

# 🌫️ Air Quality Index (AQI) Analysis — India (2015–2020)

**An end-to-end data analytics project examining air pollution patterns across 26 Indian cities**

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-1.5%2B-150458?style=flat-square&logo=pandas&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![License](https://img.shields.io/badge/Code-MIT-blue?style=flat-square)
![Data](https://img.shields.io/badge/Data-CC0%20Public%20Domain-green?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=flat-square)

[![Open in Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Team](#-team)
- [Research Questions](#-research-questions)
- [Dataset](#-dataset)
- [Project Structure](#-project-structure)
- [Notebook Walkthrough](#-notebook-walkthrough)
- [Power BI Dashboard](#-power-bi-dashboard)
- [Key Findings](#-key-findings)
- [Visualisations](#-visualisations)
- [How to Run](#-how-to-run)
- [Tech Stack](#-tech-stack)
- [Data Quality Notes](#-data-quality-notes)
- [Known Issues](#-known-issues)
- [License](#-license)
- [Attribution](#-attribution)

> 🔗 **Live Kaggle Notebook:** [kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)

---

## 🔍 Overview

India is home to some of the most polluted cities in the world. This project analyses **29,531 daily AQI observations** across **26 major Indian cities** from January 2015 to July 2020, sourced from India's Central Pollution Control Board (CPCB).

The analysis answers three core research questions around **temporal trends**, **pollutant drivers**, and **geographic exposure** — using Python-based EDA, 14 publication-quality charts, 7 summary tables, and an interactive Power BI dashboard. A dedicated COVID-19 lockdown comparison provides a natural experiment showing the direct impact of human activity on air quality.

---

## 👥 Team

| Name | Role | Primary Responsibilities |
|------|------|--------------------------|
| **Yash David Bedekar** | Project Lead & Data Engineer | Project coordination and timeline management; Kaggle environment setup and dataset integration; data ingestion pipeline; cleaning pipeline (missing value profiling, column selection, AQI row removal, outlier capping); feature engineering (`Year`, `Month`, `Season`, `DayOfWeek`, `Unhealthy`); `requirements.txt` and reproducibility |
| **Surya Koushik Palla** | Data Analyst & Visualisation Lead | All 14 publication-quality charts (Figs. 01–14); RQ1 temporal analysis — monthly trend, year-on-year chart, seasonal cycle, day-of-week analysis (with SD error bars); chart theme, colour palette, and annotation standards; interpretation print statements; ZIP export cell |
| **Aryaman Negi** | Statistical Analyst | RQ2 pollutant relationship analysis — Pearson correlation matrix, pollutant-by-category charts, PM2.5/PM10 boxplots, PM10 scatter; outlier detection and `AQI_capped` methodology; descriptive statistics (Tables 02 & 03); city-level summary statistics for all 26 cities; data quality documentation |
| **Arda Onhun** | Insights & Reporting Lead | RQ3 city exposure analysis — unhealthy day ranking, category mix chart, city × month heatmap, Table 07; COVID-19 lockdown comparison (Table 06, Fig. 07); Key Findings narrative; project report (DOCX & MD); README |

---

## ❓ Research Questions

| # | Question | Section |
|---|----------|---------|
| **RQ1** | How does AQI change over time — daily, monthly, seasonally, and year-on-year? | §7 |
| **RQ2** | Which pollutants (PM2.5, PM10, NO₂, SO₂, CO, O₃) are most strongly associated with AQI? | §8 |
| **RQ3** | How often does air reach unhealthy levels, and which cities are worst affected? | §9 |

---

## 📊 Dataset

| Attribute | Detail |
|-----------|--------|
| **Name** | Air Quality Data in India (2015–2020) |
| **Source** | Central Pollution Control Board (CPCB) |
| **Published by** | Rohan Rao on Kaggle |
| **Kaggle link** | [kaggle.com/datasets/rohanrao/air-quality-data-in-india](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india) |
| **File used** | `city_day.csv` — one row per city per day |
| **Records** | 29,531 daily observations (24,850 after cleaning) |
| **Cities** | 26 |
| **Period** | 1 January 2015 – 1 July 2020 |
| **License** | CC0 Public Domain |

> The raw CSV is not committed to this repo — see [`data/README.md`](data/README.md) for how to get it.

### Columns

| Column | Type | Unit | Description |
|--------|------|------|-------------|
| `City` | string | — | City name |
| `Date` | datetime | — | Observation date |
| `PM2.5` | float | µg/m³ | Fine particulate matter (≤2.5 µm) |
| `PM10` | float | µg/m³ | Coarse particulate matter (≤10 µm) |
| `NO2` | float | µg/m³ | Nitrogen dioxide |
| `SO2` | float | µg/m³ | Sulphur dioxide |
| `CO` | float | mg/m³ | Carbon monoxide |
| `O3` | float | µg/m³ | Ground-level ozone |
| `AQI` | float | Index | Air Quality Index (CPCB scale 0–500+) |
| `AQI_Bucket` | string | — | Category label (Good → Severe) |

### CPCB AQI Scale

| AQI Range | Category | Health Concern | Unhealthy? |
|-----------|----------|----------------|------------|
| 0 – 50 | Good | Minimal impact | No |
| 51 – 100 | Satisfactory | Minor discomfort to sensitive individuals | No |
| 101 – 200 | Moderate | Breathing discomfort for lung/heart patients | No |
| 201 – 300 | **Poor** | Discomfort for most on prolonged exposure | **Yes** |
| 301 – 400 | **Very Poor** | Respiratory illness on prolonged exposure | **Yes** |
| 401 – 500 | **Severe** | Serious impact on healthy people | **Yes** |

> This project defines **"unhealthy"** as AQI > 200, following CPCB guidelines.

---

## 📁 Project Structure

```
air-quality-index-project/
│
├── 📄 README.md                        # This file
├── 📜 LICENSE                           # MIT (code/report) — dataset itself is CC0
├── 🚫 .gitignore
├── 📋 requirements.txt                  # Python dependencies
│
├── 📂 data/
│   └── README.md                       # Where to download city_day.csv (not committed)
│
├── 📂 notebook/
│   └── air-quality-index-aqi-analysis.ipynb   # Main analysis notebook (24 code cells, 11 sections)
│
├── 📂 figures/                          # 14 charts generated by the notebook
│   ├── fig01_monthly_trend.png
│   ├── fig02_yearly_trend.png
│   ├── fig03_bucket_distribution.png
│   ├── fig04_monthly_cycle.png
│   ├── fig05_season.png
│   ├── fig06_dayofweek.png
│   ├── fig07_covid_lockdown.png
│   ├── fig08_corr_heatmap.png
│   ├── fig09_pollutant_by_category.png
│   ├── fig10_pm_boxplots.png
│   ├── fig11_pm10_scatter.png
│   ├── fig12_unhealthy_rank.png
│   ├── fig13_category_mix.png
│   └── fig14_unhealthy_heatmap.png
│
├── 📂 tables/                           # 7 CSV tables generated by the notebook
│   ├── table_01_missing_values.csv
│   ├── table_02_summary_stats.csv
│   ├── table_03_city_stats.csv
│   ├── table_04_yearly_aqi.csv
│   ├── table_05_bucket_distribution.csv
│   ├── table_06_covid_comparison.csv
│   └── table_07_unhealthy_summary.csv
│
├── 📂 powerbi/                          # Interactive dashboard companion
│   ├── README.md                       # Data model, DAX measures, build steps
│   └── dataset/                        # 10 ready-to-import CSVs (see powerbi/README.md)
│
├── 📂 reports/
│   ├── AQI_Analysis_Report.md          # Full project report (Markdown)
│   └── AQI_Analysis_Report.docx        # Full project report (Word)
│
└── 📂 presentation/
    ├── AQI_Analysis_Presentation.pptx
    └── PRESENTATION_VIDEO_LINK.md      # ⚠️ see Known Issues — link needs replacing
```

> 🔗 **Live Kaggle Notebook:** [kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)

---

## 📓 Notebook Walkthrough

| Section | Title | Description |
|---------|-------|-------------|
| **§4** | Setup | Imports, global constants (`POLLUTANTS`, `FOCUS`, `CAT_ORDER`, `CAT_COLORS`), plot theme, Kaggle/local path auto-detection |
| **§5** | Load Dataset | Reads `city_day.csv`, prints shape/date range/city list, displays raw sample |
| **§6** | Data Cleaning | Missing value profiling → column selection → AQI row removal → outlier detection & capping → feature engineering → CPCB reference table |
| **§7** | RQ1 — Temporal | Monthly trend + COVID-19 annotation · Year-on-year chart + table · AQI bucket distribution · Seasonal cycle · Season averages · Day-of-week with ±1 SD · Lockdown before/after comparison |
| **§8** | RQ2 — Pollutants | Correlation heatmap (lower triangle) · Mean pollutant by AQI category · PM2.5/PM10 boxplots · PM10 vs AQI scatter (n = 5,000 sampled) |
| **§9** | RQ3 — Unhealthy | Top-12 city ranking · Stacked category-mix bar · City × month heatmap · Unhealthy-day summary table |
| **§10** | Key Findings | Written narrative covering all three RQs with data-quality caveats |
| **§11** | Export | Zips all 21 output files (14 figures + 7 tables) into `AQI_Analysis_Outputs.zip` |

---

## 📊 Power BI Dashboard

An interactive companion to the static notebook charts lives in [`powerbi/`](powerbi). It ships
a 10-table, ready-to-import dataset (derived from the notebook's own verified tables and report
figures — no new data sourced from outside this project) plus a full build spec:

- **[`powerbi/dataset/`](powerbi/dataset)** — `dim_city_stats.csv` (26 cities, enriched with
  region + coordinates for a map visual) and 9 `fact_*.csv` tables covering yearly trends, AQI
  category mix, the COVID-19 lockdown comparison, day-of-week/seasonal averages, pollutant
  correlations, and data-quality metrics.
- **[`powerbi/README.md`](powerbi/README.md)** — table-by-table data lineage, the model
  relationships to set up, suggested DAX measures, and a 4-page report layout (Overview ·
  Temporal RQ1 · Pollutants RQ2 · City Exposure RQ3).

Power BI Desktop is required to open/build the `.pbix` (not committed — see the guide for why);
following the build steps takes about 10–15 minutes.

---

## 💡 Key Findings

### RQ1 — Temporal Patterns

- **Air quality is strongly seasonal.** Average AQI peaks in **Winter (Dec–Jan)** due to temperature inversions, and drops sharply during the **Monsoon (Jul–Aug)** when rainfall washes out particulates.
- **A clear north–south divide.** Delhi and Lucknow consistently exceed AQI 200; Bengaluru, Chennai, and Hyderabad remain below AQI 150 for most of the year.
- **Day-of-week variation is negligible.** Seasonal weather — not weekday traffic — is the dominant driver.
- **COVID-19 lockdown (Apr–Jun 2020) caused a measurable AQI drop in every city**, confirming the human contribution to urban air pollution.

### RQ2 — Pollutant Relationships

- **PM10 has the strongest correlation with AQI (r = 0.80)**, followed by CO (r = 0.69) and PM2.5 (r = 0.67).
- **Ozone (O₃) is weakly correlated (r ≈ 0.20)** — not a dominant AQI driver in India.
- **PM2.5 and PM10 concentrations rise monotonically** from *Good* to *Severe*, validating the CPCB category scheme.

### RQ3 — Unhealthy Conditions

- **~26% of all city-days are unhealthy (AQI > 200)** — poor air quality is systemic, not occasional.
- **Ahmedabad (~82%) and Delhi (~65%)** have the highest share of unhealthy days. Ahmedabad's figure is partially inflated by data-quality outliers.
- **Southern/coastal cities** (Bengaluru ~2%, Hyderabad ~4%, Mumbai ~5%) are the least affected.
- **Worst exposure window: northern cities, October–January** — driven by stubble burning, cold inversions, and Diwali fireworks.

---

## 📈 Visualisations

| Figure | Title | Key Insight |
|--------|-------|-------------|
| Fig 01 | Monthly AQI Trend (2015–2020) | Clear winter peaks; COVID-19 dip visible in 2020 |
| Fig 02 | Year-on-Year AQI by City | Delhi and Lucknow most volatile; 2020 lockdown drop |
| Fig 03 | AQI Bucket Distribution | Moderate & Satisfactory are the most common categories |
| Fig 04 | Seasonal Cycle by Month | U-shaped curve — peaks Dec–Jan, trough Jul–Aug |
| Fig 05 | Average AQI by Season | Winter > Post-Monsoon > Summer > Monsoon |
| Fig 06 | AQI by Day of Week | Negligible variation — weather drives pollution, not schedules |
| Fig 07 | COVID-19 Lockdown Impact | Every city improved Apr–Jun 2020 vs Jan–Mar 2020 |
| Fig 08 | Correlation Heatmap | PM10 strongest (r = 0.80); O₃ weakest (r = 0.20) |
| Fig 09 | Pollutants by AQI Category | PM2.5/PM10 rise steeply; SO₂/NO₂ rise modestly |
| Fig 10 | PM2.5 & PM10 Boxplots | Medians increase monotonically across all 6 categories |
| Fig 11 | PM10 vs AQI Scatter | Strong positive trend; Severe days at PM10 > 200 µg/m³ |
| Fig 12 | Top 12 Cities — Unhealthy Days | Ahmedabad and Delhi far ahead of others |
| Fig 13 | AQI Category Mix by City | Visual north–south divide in category distribution |
| Fig 14 | City × Month Heatmap | Northern cities worst in Oct–Jan; south near-zero year-round |

---

## 🚀 How to Run

### Option A — Kaggle (Recommended, Zero Setup)

The notebook is published and ready to run on Kaggle:

**👉 [kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)**

1. Open the link above and click **Copy & Edit** to fork it into your Kaggle account.
2. The dataset is already attached — the notebook auto-detects the Kaggle input path (`/kaggle/input/datasets/rohanrao/air-quality-data-in-india/city_day.csv`).
3. Click **Run All** — no setup or file downloads required.

### Option B — Local (Jupyter)

**Step 1 — Clone the repository**
```bash
git clone https://github.com/yashbedekar07/air-quality-index-project.git
cd air-quality-index-project
```

**Step 2 — Install dependencies**
```bash
pip install -r requirements.txt
```

**Step 3 — Download the dataset**

Download `city_day.csv` from [Kaggle](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india) and save it to:
```
data/city_day.csv
```

**Step 4 — Launch the notebook**
```bash
jupyter notebook notebook/air-quality-index-aqi-analysis.ipynb
```

Run all cells top to bottom. The notebook creates `figures/` (14 charts) and `tables/` (7 CSVs),
and generates `AQI_Analysis_Outputs.zip` in the final cell.

### Option C — Power BI Dashboard

No Python needed — see [`powerbi/README.md`](powerbi/README.md) for the full build guide
(import the CSVs in `powerbi/dataset/`, wire up the relationships, add the DAX measures, build
the 4 report pages). Takes about 10–15 minutes in Power BI Desktop.

---

## 🛠️ Tech Stack

| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.10+ | Core language |
| pandas | 1.5+ | Data loading, cleaning, aggregation |
| NumPy | 1.23+ | Numerical operations |
| Matplotlib | 3.6+ | Base plotting engine |
| Seaborn | 0.12+ | Statistical visualisations |
| Jupyter | 1.0+ | Interactive notebook environment |
| Power BI Desktop | 2024+ | Interactive dashboard companion |

```bash
pip install -r requirements.txt
```

---

## ⚠️ Data Quality Notes

1. **Missing values are real monitoring gaps.** Xylene (61.3% missing), PM10 (37.7%), and NH3 (35.0%) have the highest missingness. Pollutant NaNs are left as-is; all functions use pairwise-complete observations.

2. **Ahmedabad AQI outliers.** Raw AQI values up to 2,049 (above the CPCB ceiling of 500) likely reflect sensor malfunctions. The notebook creates `AQI_capped` (clipped at 500) for all aggregate statistics.

3. **2020 data is partial.** Records run to July 2020 only. Year-on-year comparisons for 2020 reflect January–July and are also influenced by the COVID-19 lockdown.

4. **Community-curated dataset.** The Kaggle version is compiled from CPCB monitoring stations and has not undergone official CPCB quality assurance.

---

## 🐞 Known Issues

- **Presentation video link requires organisational sign-in.** [`presentation/PRESENTATION_VIDEO_LINK.md`](presentation/PRESENTATION_VIDEO_LINK.md) currently points to a Microsoft Teams meeting-recap URL scoped to the BITS Iserlohn tenant — it will not open for a viewer outside that organisation. **Action needed before final submission:** replace it with a public link (e.g. an unlisted YouTube upload or a public OneDrive/Drive share).
- **Power BI `.pbix` is not pre-built.** The dataset and full build spec are ready in [`powerbi/`](powerbi), but the binary report file itself needs to be assembled once in Power BI Desktop (~10–15 min) — see [`powerbi/README.md`](powerbi/README.md).

---

## 📜 License

The analysis code, notebook, report, and Power BI assets in this repository are released under
the [MIT License](LICENSE). The underlying dataset remains under its original **CC0 Public
Domain** license from CPCB / Rohan Rao (Kaggle) — see [Dataset](#-dataset).

---

## 📜 Attribution

- **Data:** Central Pollution Control Board (CPCB), Government of India — [cpcb.nic.in](https://cpcb.nic.in)
- **Dataset curator:** Rohan Rao — published on Kaggle under CC0 Public Domain
- **Live Notebook:** [kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)
- **Analysis:** Conducted for educational, non-commercial purposes only

---

<div align="center">

**Team:** Yash David Bedekar &nbsp;·&nbsp; Surya Koushik Palla &nbsp;·&nbsp; Aryaman Negi &nbsp;·&nbsp; Arda Onhun

*DA 120B — Data Analytics · Academic Year 2025–2026*

Made with 🐍 Python &nbsp;·&nbsp; 📊 pandas &nbsp;·&nbsp; 📈 Matplotlib &nbsp;·&nbsp; 🎨 Seaborn &nbsp;·&nbsp; 📊 Power BI

</div>

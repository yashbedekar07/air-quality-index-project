# Air Quality Index (AQI) Analysis — India (2015–2020)
## Project Report | DA 120B | Data Analytics

> **Live Notebook:** [kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)  
> **Dataset:** Air Quality Data in India (2015–2020) · CPCB via Kaggle (rohanrao) · CC0 Public Domain  
> **Records:** 29,531 raw · 24,850 after cleaning · 26 cities · Jan 2015 – Jul 2020

---

## Team Members & Roles

| Name | Role | Primary Responsibilities |
|------|------|--------------------------|
| **Yash David Bedekar** | Project Lead & Data Engineer | Project coordination; Kaggle notebook setup & publication; data ingestion pipeline; cleaning pipeline (missing values, column selection, outlier capping, AQI row removal); feature engineering; `requirements.txt` |
| **Surya Koushik Palla** | Data Analyst & Visualisation Lead | All 14 charts (Figs. 01–14); RQ1 temporal analysis; chart theme, `CAT_COLORS` palette, annotation standards; interpretation print statements; ZIP export cell |
| **Aryaman Negi** | Statistical Analyst | RQ2 pollutant correlation analysis; PM2.5/PM10 boxplots & PM10 scatter; outlier detection & `AQI_capped` methodology; descriptive stats (Tables 02 & 03); all-26-city summary; data quality documentation |
| **Arda Onhun** | Insights & Reporting Lead | RQ3 city exposure analysis; COVID-19 lockdown comparison (Table 06, Fig. 07); key findings narrative; project report (DOCX & MD); README |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Introduction](#2-introduction)
3. [Dataset](#3-dataset)
4. [Methodology](#4-methodology)
5. [Findings — RQ1: Temporal Trends](#5-findings--rq1-temporal-trends)
6. [Findings — RQ2: Pollutant Relationships](#6-findings--rq2-pollutant-relationships)
7. [Findings — RQ3: Unhealthy Air Quality Exposure](#7-findings--rq3-unhealthy-air-quality-exposure)
8. [Discussion](#8-discussion)
9. [Conclusion](#9-conclusion)
10. [References](#10-references)

---

## 1. Executive Summary

This report presents a comprehensive data analytics study of Air Quality Index (AQI) measurements across **26 major Indian cities** from January 2015 to July 2020, using **29,531 daily observations** (24,850 after cleaning) from India's Central Pollution Control Board (CPCB).

| Research Question | Core Finding |
|-------------------|-------------|
| **RQ1 — Temporal** | Strong seasonal cycle: Winter avg AQI ~221, Monsoon ~116. COVID-19 lockdown produced AQI drops in 25 of 26 cities. Day-of-week variation negligible (158–164 range). |
| **RQ2 — Pollutants** | PM10 is the dominant AQI driver (r = 0.80), followed by CO (r ≈ 0.69) and PM2.5 (r ≈ 0.67). Ozone is weakly correlated (r ≈ 0.20). |
| **RQ3 — Exposure** | **26.0%** of city-days are unhealthy (AQI > 200). Moderate (35.5%) is the single most common category. Delhi (65.1%) is the worst-hit major city with verified data. |

---

## 2. Introduction

### 2.1 Background & Motivation

India is home to some of the world's most polluted cities. The Air Quality Index (AQI) is the primary metric used by India's CPCB to communicate daily air quality. Understanding how AQI varies across cities, seasons, and years — and which pollutants drive it — is essential for designing effective interventions.

### 2.2 Research Questions

1. **RQ1 — Temporal Trends:** How does AQI change over time? Are there clear daily, monthly, seasonal, or year-on-year patterns?
2. **RQ2 — Pollutant Relationships:** Is there a measurable relationship between specific pollutants (PM2.5, PM10, NO₂, SO₂, CO, O₃) and the overall AQI category?
3. **RQ3 — Unhealthy Conditions:** How often does air quality reach unhealthy or hazardous levels, and which cities are most affected?

### 2.3 Objectives

- Profile air quality trends over time and identify seasonal patterns across 26 cities.
- Quantify the statistical relationship between six core pollutants and the AQI.
- Identify cities with the highest exposure to unhealthy air and the months when risk peaks.
- Analyse the COVID-19 national lockdown (March 2020) as a natural experiment in pollution reduction.
- Produce clean, reproducible analysis code and publication-quality visualisations.

---

## 3. Dataset

### 3.1 Source & Overview

| Attribute | Detail |
|-----------|--------|
| Dataset name | Air Quality Data in India (2015–2020) |
| Primary source | Central Pollution Control Board (CPCB), Government of India |
| Curator | Rohan Rao (Kaggle: `rohanrao`) |
| Kaggle dataset | https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india |
| **Live notebook** | **https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis** |
| File used | `city_day.csv` — one row per city per day |
| Total records | 29,531 daily observations |
| Records after cleaning | 24,850 (4,681 rows with missing AQI removed) |
| Cities covered | 26 major Indian cities |
| Time period | 1 January 2015 – 1 July 2020 |
| Licence | CC0 Public Domain |

### 3.2 CPCB AQI Scale

This project defines **"unhealthy"** as AQI > 200 (Poor, Very Poor, and Severe).

| AQI Range | Category | Health Concern | Unhealthy? |
|-----------|----------|----------------|------------|
| 0 – 50 | Good | Minimal impact | No |
| 51 – 100 | Satisfactory | Minor discomfort to sensitive individuals | No |
| 101 – 200 | Moderate | Breathing discomfort for lung/heart patients | No |
| 201 – 300 | **Poor** | Discomfort for most on prolonged exposure | **Yes** |
| 301 – 400 | **Very Poor** | Respiratory illness on prolonged exposure | **Yes** |
| 401 – 500 | **Severe** | Serious impact on healthy people | **Yes** |

---

## 4. Methodology

### 4.1 Tools & Environment

| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.10+ | Core programming language |
| pandas | 1.5+ | Data loading, cleaning, aggregation |
| NumPy | 1.23+ | Numerical operations |
| Matplotlib | 3.6+ | Base charting engine |
| Seaborn | 0.12+ | Statistical visualisations |
| Jupyter | 1.0+ | Interactive notebook environment |
| Kaggle | Cloud | Execution environment — [notebook link](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis) |

### 4.2 Data Cleaning Pipeline

1. **Missing value profiling** — Xylene (61.3% missing), PM10 (37.7%), NH3 (35.0%) had the highest gaps.
2. **Column selection** — Retained City, Date, six core pollutants, AQI, AQI_Bucket. Dropped trace-gas columns.
3. **AQI row removal** — 4,681 rows (15.9%) dropped, leaving **24,850 analysis-ready records**.
4. **Outlier detection & capping** — AQI > 500 flagged; `AQI_capped` column (clipped at 500) used for aggregates. Ahmedabad raw max = 2,049.
5. **Feature engineering** — `Year`, `Month`, `DayOfWeek`, `Season`, `Unhealthy` (AQI > 200) derived.
6. **Imputation** — Pollutant NaNs left as-is; all functions use pairwise-complete observations.

---

## 5. Findings — RQ1: Temporal Trends

### 5.1 Monthly AQI Trend (2015–2020) — *Fig. 01*

Delhi and Lucknow exceed AQI 200 every winter. Southern cities (Bengaluru, Chennai, Hyderabad, Mumbai) remain flat year-round. A clear dip in April–June 2020 marks the COVID-19 lockdown (25 March 2020).

### 5.2 Year-on-Year Trend — *Fig. 02, Table 04*

Delhi's annual mean AQI (capped) fluctuated between ~230–290 with no clear downward trend. Southern cities remained stable below AQI 200. The 2020 figures are partial (January–July only) and influenced by the lockdown.

### 5.3 AQI Category Distribution — *Fig. 03, Table 05*

**Actual notebook output:**

| AQI Category | City-Days | Percentage | Unhealthy? |
|--------------|-----------|------------|------------|
| Good | 1,341 | 5.4% | No |
| Satisfactory | 8,224 | 33.1% | No |
| **Moderate** | **8,829** | **35.5%** | No |
| Poor | 2,781 | 11.2% | **Yes** |
| Very Poor | 2,337 | 9.4% | **Yes** |
| Severe | 1,338 | 5.4% | **Yes** |
| **Total unhealthy** | **6,456** | **26.0%** | **Yes** |

Moderate (35.5%) is the single most common category. The combined unhealthy categories (Poor + Very Poor + Severe) account for **26.0%** of all city-days.

### 5.4 Seasonal Cycle — *Fig. 04, Fig. 05*

| Season | Months | Avg. AQI (capped) | Primary Driver |
|--------|--------|-------------------|----------------|
| Winter | Dec, Jan, Feb | **~221** | Temperature inversions trapping particulates |
| Post-Monsoon | Oct, Nov | **~215** | Stubble burning; Diwali fireworks |
| Summer | Mar, Apr, May | ~158 | Dry but windy conditions |
| Monsoon | Jun, Jul, Aug, Sep | **~116** | Rainfall washing out particulates |

The monsoon–winter AQI difference of ~105 points represents the difference between Satisfactory and Poor categories on average.

### 5.5 Day-of-Week Pattern — *Fig. 06*

**Actual notebook output (all cities, AQI capped):**

| Day | Avg AQI (capped) |
|-----|-----------------|
| Monday | 158 |
| Tuesday | 161 |
| Wednesday | 162 |
| Thursday | **164** |
| Friday | 163 |
| Saturday | 160 |
| Sunday | 159 |

All seven days fall within a **6-point range (158–164)**. With fully overlapping ±1 SD error bars, day-of-week variation is statistically negligible. Seasonal weather — not weekday traffic — is the dominant driver.

### 5.6 COVID-19 Lockdown Impact — *Fig. 07, Table 06*

**Selected city results from actual notebook output (pre-lockdown = Jan–Mar 2020, lockdown = Apr–Jun 2020):**

| City | Pre-Lockdown AQI | Lockdown AQI | Change % |
|------|-----------------|-------------|----------|
| Delhi | 234.9 | 129.3 | −45.5% |
| Lucknow | 207.6 | 107.6 | −48.2% |
| Mumbai | 143.8 | 63.9 | −55.6% |
| Kolkata | 178.8 | 56.6 | −68.4% |
| Bengaluru | 94.1 | 65.7 | −30.2% |
| Hyderabad | 92.7 | 64.0 | −30.9% |
| Chennai | 78.7 | 81.7 | +3.8% (slight increase) |
| Guwahati | 250.3 | 74.1 | −70.4% |

25 of 26 cities recorded AQI improvements during the lockdown, confirming anthropogenic emissions are a significant and reducible pollution driver.

> **Key Takeaway (RQ1):** Air quality follows a strong seasonal cycle (Winter ~221 vs Monsoon ~116). Day-of-week effects are negligible (158–164 range). COVID-19 lockdown produced measurable reductions in 25/26 cities.

---

## 6. Findings — RQ2: Pollutant Relationships

### 6.1 Correlation Analysis — *Fig. 08*

| Pollutant | Pearson r with AQI | Strength | Interpretation |
|-----------|-------------------|----------|----------------|
| PM10 | **0.80** | Very Strong | Primary AQI driver |
| CO | 0.69 | Strong | Co-located with PM episodes |
| PM2.5 | 0.67 | Strong | Key health risk and predictor |
| NO2 | 0.53 | Moderate | Traffic-sourced |
| SO2 | 0.38 | Moderate | Industrial source |
| O3 | 0.20 | Weak | Not a primary AQI driver in India |

### 6.2 Pollutant Concentrations by Category — *Fig. 09*

PM2.5 and PM10 show monotonically increasing concentrations from Good to Severe, validating the CPCB category scheme. Ozone peaks at Moderate and declines in Severe, reflecting complex photochemical dynamics.

### 6.3 PM2.5 & PM10 Distributions — *Fig. 10*

Boxplot medians increase at every step of the CPCB scale. The widening IQR in higher categories reflects episodic events (stubble burning, dust storms) driving the upper tail.

### 6.4 PM10 vs. AQI Scatter — *Fig. 11*

Good/Satisfactory days cluster at PM10 < 100 µg/m³. Severe days consistently appear at PM10 > 200 µg/m³.

> **Key Takeaway (RQ2):** PM10 (r = 0.80) and PM2.5 (r = 0.67) dominate AQI. Ozone (r = 0.20) is not a primary driver. Reducing particulate emissions is the highest-leverage policy intervention.

---

## 7. Findings — RQ3: Unhealthy Air Quality Exposure

### 7.1 Overall Exposure Rate

**6,456 of 24,850 valid city-days (26.0%)** are unhealthy (AQI > 200). Poor air quality is a persistent baseline condition, not a rare extreme event.

### 7.2 City Rankings — *Fig. 12, Table 07*

**Actual notebook output (Table 07):**

| City | Total Days | Mean AQI (capped) | Median AQI | Max AQI (raw) | Unhealthy Days | Unhealthy % |
|------|------------|-------------------|------------|---------------|----------------|-------------|
| Ahmedabad ⚠️ | 1,334 | 356.0 | 384.5 | 2,049 | 1,092 | **81.9%** |
| Delhi | 1,999 | 258.1 | 257.0 | 716 | 1,301 | **65.1%** |
| Lucknow | 1,893 | 217.5 | 198.0 | 707 | 935 | 49.4% |
| Kolkata | 754 | 140.6 | 94.0 | 475 | 198 | 26.3% |
| Chennai | 1,884 | 114.5 | 100.0 | 449 | 127 | 6.7% |
| Mumbai | 775 | 105.4 | 91.0 | 307 | 36 | 4.6% |
| Hyderabad | 1,880 | 109.0 | 104.0 | 737 | 71 | 3.8% |
| Bengaluru | 1,910 | 94.3 | 86.0 | 352 | 41 | 2.1% |

> ⚠️ **Ahmedabad note:** Raw AQI up to 2,049 (above CPCB max of 500) indicates sensor malfunctions. Its capped mean AQI of 356.0 still reflects a genuine but overstated pollution problem.

### 7.3 Seasonal Exposure — *Fig. 14*

The city × month heatmap shows October–January carries near-100% unhealthy day rates for Delhi and Lucknow. Southern cities show near-zero unhealthy days year-round.

### 7.4 AQI Category Mix — *Fig. 13*

Bengaluru and Mumbai are dominated by green (Good/Satisfactory) bars. Delhi and Lucknow show large red/dark-red segments for Very Poor and Severe.

> **Key Takeaway (RQ3):** 26.0% of city-days are unhealthy. Moderate (35.5%) is the most common single category. The risk is concentrated in northern cities during October–January. Southern cities (Bengaluru 2.1%, Hyderabad 3.8%) experience minimal unhealthy exposure year-round.

---

## 8. Discussion

### 8.1 North–South Divide

The geographic contrast is driven by the Indo-Gangetic Plain's flat terrain and cold winter inversions, large-scale agricultural burning in October–November, and the higher industrial density of northern cities. Southern cities benefit from sea breezes, higher temperatures, and better meteorological dispersion.

### 8.2 Particulate Matter as the Priority Pollutant

PM10 (r = 0.80) and PM2.5 (r = 0.67) dominate the AQI signal. Interventions targeting particulate emissions — vehicle exhaust standards, industrial controls, stubble burning bans, construction dust suppression — will have the greatest leverage on measured AQI.

### 8.3 COVID-19 as a Natural Experiment

The lockdown-period AQI drops quantify the anthropogenic contribution: Mumbai −55.6%, Kolkata −68.4%, Delhi −45.5%, Guwahati −70.4%. Even Chennai, which showed a slight increase (+3.8%), was likely a seasonal effect rather than true counter-evidence. These figures suggest targeted short-term emission reduction interventions can produce rapid, measurable improvements.

### 8.4 Limitations

- Dataset ends July 2020; long-term trend analysis and post-lockdown recovery cannot be assessed.
- Ahmedabad data quality issues (raw max AQI = 2,049) inflate its statistics.
- City-level analysis only; within-city neighbourhood variation not accessible.
- Correlation identifies associations, not causation. Meteorological confounders not controlled.
- Unequal city record counts (Kolkata: 754 vs Delhi: 1,999 days) create unequal statistical power.

---

## 9. Conclusion

This project delivered a comprehensive, reproducible data analytics study using 24,850 valid city-days across 26 Indian cities from 2015 to 2020.

**Three core conclusions:**

1. **Air quality is governed primarily by seasonal meteorology.** Winter temperature inversions and post-monsoon stubble burning produce the worst episodes (avg AQI ~221 and ~215 respectively). The monsoon provides natural cleansing (~116). Day-of-week patterns are negligible (158–164).

2. **Particulate matter (PM10 r=0.80, PM2.5 r=0.67) and CO (r=0.69) are the dominant AQI drivers.** Ozone (r=0.20) is not a primary driver in India. Policy should prioritise particulate emission sources.

3. **26.0% of city-days are unhealthy (AQI > 200).** Moderate (35.5%) is the most common single category. The risk is concentrated in northern cities (Delhi 65.1%, Lucknow 49.4%) during October–January. Southern cities experience minimal unhealthy exposure year-round.

The COVID-19 natural experiment confirmed that anthropogenic emissions are significant and reducible — 25 of 26 cities improved during the lockdown period.

---

## 10. References

1. Central Pollution Control Board (CPCB). *National Air Quality Index.* Government of India. https://cpcb.nic.in
2. Rao, R. (2020). *Air Quality Data in India (2015–2020)* [Dataset]. Kaggle. https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india
3. Bedekar, Y. D. (2026). *Air Quality Index (AQI) Analysis — India (2015–2020)* [Kaggle Notebook]. https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis
4. McKinney, W. (2010). Data Structures for Statistical Computing in Python. *Proceedings of the 9th Python in Science Conference.* pandas development team.
5. Hunter, J. D. (2007). Matplotlib: A 2D Graphics Environment. *Computing in Science & Engineering, 9*(3), 90–95.
6. Waskom, M. (2021). Seaborn: Statistical Data Visualization. *Journal of Open Source Software, 6*(60), 3021.
7. WHO (2021). *WHO Global Air Quality Guidelines.* World Health Organization.

---

*Report prepared by: Yash David Bedekar · Surya Koushik Palla · Aryaman Negi · Arda Onhun*  
*Course: DA 120B — Data Analytics · Academic Year 2025–2026*  
*Live Notebook: [kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis](https://www.kaggle.com/code/yashbedekar07/air-quality-index-aqi-analysis)*

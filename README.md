# DataAnalytics-Project-120B

# 🌬️ Air Quality Index Analysis — DA 120B

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?logo=python)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)
![License](https://img.shields.io/badge/License-MIT-green)
![Course](https://img.shields.io/badge/Course-DA%20120B-purple)

> A comprehensive data analytics project analyzing Air Quality Index (AQI) trends, pollutant relationships, and hazardous conditions across multiple locations and time periods.

---

## 📌 Project Overview

This project examines AQI data to uncover temporal patterns, pollutant correlations, and high-risk locations. The analysis covers major pollutants including **PM2.5**, **PM10**, **NO2**, **SO2**, **CO**, and **O3**, and applies data analytics techniques to produce actionable environmental insights.

---

## ❓ Research Questions

| # | Question |
|---|----------|
| 1 | How does AQI change over time — are there daily, monthly, or seasonal patterns? |
| 2 | Is there a relationship between specific pollutants (PM2.5, PM10, NO2, SO2, CO, O3) and the overall AQI category? |
| 3 | How often does air quality reach unhealthy or hazardous levels, and which locations are most affected? |

---

## 🗂️ Repository Structure

```
air-quality-project/
│
├── data/
│   ├── raw/                    # Original, unmodified AQI datasets
│   └── processed/              # Cleaned and transformed data files
│
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_eda_temporal.ipynb
│   ├── 04_pollutant_correlation.ipynb
│   └── 05_hazardous_analysis.ipynb
│
├── scripts/
│   ├── data_prep/
│   │   ├── load_data.py
│   │   └── clean_data.py
│   ├── analysis/
│   │   ├── temporal_analysis.py
│   │   ├── correlation_analysis.py
│   │   └── location_analysis.py
│   └── visualization/
│       ├── plot_aqi_trends.py
│       ├── plot_heatmaps.py
│       └── plot_pollutants.py
│
├── reports/
│   ├── figures/                # All generated charts and plots
│   ├── tables/                 # Summary tables and statistics
│   └── final_report.pdf
│
├── docs/
│   └── data_dictionary.md      # Variable definitions and AQI category thresholds
│
├── requirements.txt
├── .gitignore
└── README.md
```

---

## 📊 Dataset Description

The dataset contains historical AQI measurements across multiple geographic locations, with the following key variables:

| Column | Description |
|--------|-------------|
| `Date` | Measurement date (YYYY-MM-DD) |
| `Location` | City or monitoring station name |
| `AQI` | Composite Air Quality Index score |
| `AQI_Category` | Good / Moderate / Unhealthy / Hazardous |
| `PM2.5` | Fine particulate matter (µg/m³) |
| `PM10` | Coarse particulate matter (µg/m³) |
| `NO2` | Nitrogen dioxide (ppb) |
| `SO2` | Sulfur dioxide (ppb) |
| `CO` | Carbon monoxide (ppm) |
| `O3` | Ozone (ppb) |

### AQI Category Thresholds

| AQI Range | Category | Color Code |
|-----------|----------|------------|
| 0–50 | Good | 🟢 Green |
| 51–100 | Moderate | 🟡 Yellow |
| 101–150 | Unhealthy for Sensitive Groups | 🟠 Orange |
| 151–200 | Unhealthy | 🔴 Red |
| 201–300 | Very Unhealthy | 🟣 Purple |
| 301+ | Hazardous | 🟤 Maroon |

---

## 🔧 Setup & Installation

### Prerequisites
- Python 3.9+
- pip or conda

### Install Dependencies

```bash
git clone https://github.com/your-username/air-quality-project.git
cd air-quality-project
pip install -r requirements.txt
```

### Run Notebooks

```bash
jupyter notebook notebooks/
```

---

## 📦 Requirements

```
pandas>=1.5.0
numpy>=1.23.0
matplotlib>=3.6.0
seaborn>=0.12.0
scikit-learn>=1.1.0
jupyter>=1.0.0
scipy>=1.9.0
plotly>=5.11.0
```

---

## 📈 Key Analyses

### 1. Temporal AQI Trends
- Daily AQI time series plots
- Monthly averages and rolling means
- Seasonal decomposition (STL / moving averages)

### 2. Pollutant Correlation
- Pearson and Spearman correlation matrices
- Scatter plots of PM2.5 / PM10 vs AQI
- Feature importance ranking

### 3. Hazardous Location Analysis
- Frequency of Unhealthy/Hazardous days by city
- Heatmaps of AQI categories over time
- Top 5 most polluted locations

---

## 📋 Methodology

```
1. Data Collection  →  2. Data Cleaning  →  3. EDA  →  4. Analysis  →  5. Visualization  →  6. Reporting
```

1. **Data Collection** — Load raw AQI dataset from CSV/API
2. **Data Cleaning** — Handle missing values, outliers, and format dates
3. **Exploratory Data Analysis** — Descriptive statistics, distributions
4. **Analysis** — Temporal trends, correlations, and location-based patterns
5. **Visualization** — Charts, heatmaps, and interactive plots
6. **Reporting** — Summary findings and recommendations

---

## 👤 Author

**Data Analytics Student — DA 120B**  
Course Project | Semester Analysis  

---

## 📄 License

This project is licensed under the MIT License. See `LICENSE` for details.

# Air Quality Index (AQI) Analysis — India, 2015–2020

A data-analytics project exploring how air quality varies across major Indian cities, what drives it, and how often air becomes unhealthy. The analysis covers daily AQI readings and six core pollutants for 26 cities, with a Jupyter notebook for the full workflow and a slide deck summarising the findings.

## Research questions

1. **Temporal trends** — How does AQI change over time? Are there clear daily, monthly, or seasonal patterns?
2. **Pollutant relationships** — Is there a relationship between PM2.5, PM10, NO₂, SO₂, CO, O₃ and the overall AQI category?
3. **Unhealthy conditions** — How often does air reach unhealthy or hazardous levels, and which cities are most affected?

## Dataset

The project uses the **"Air Quality Data in India (2015–2020)"** dataset, compiled from India's **Central Pollution Control Board (CPCB)** and published openly on Kaggle. The file analysed here (`city_day.csv`) was obtained from a public GitHub mirror of the same dataset — identical data, openly accessible without an account.

- **Records:** 29,531 daily observations
- **Cities:** 26
- **Period:** 1 January 2015 – 1 July 2020
- **Columns:** `City`, `Date`, `PM2.5`, `PM10`, `NO`, `NO2`, `NOx`, `NH3`, `CO`, `SO2`, `O3`, `Benzene`, `Toluene`, `Xylene`, `AQI`, `AQI_Bucket`
- **AQI categories (CPCB):** Good · Satisfactory · Moderate · Poor · Very Poor · Severe

The data is real monitoring output, so pollutant columns contain genuine missing values that are handled during cleaning. Note: the Kaggle version is a community-curated compilation of the official CPCB readings rather than an official CPCB release file.

## Repository structure

```
aqi-analysis/
├── README.md
├── AQI_Analysis.ipynb              # Full analysis notebook (data prep, EDA, findings)
├── AQI_Analysis_Presentation.pptx  # 12-slide summary deck
└── data/
    └── city_day.csv                # The dataset
```

## What the notebook does

1. **Load & clean** — reads the data, profiles missing values, drops rows without an AQI value, and sets aside trace columns.
2. **Feature engineering** — derives Year, Month, Season, and Day-of-Week; applies the CPCB category ordering; and flags "unhealthy" days as AQI > 200.
3. **RQ1 — Temporal** — monthly/seasonal trends, calendar-month cycle, and day-of-week comparison.
4. **RQ2 — Pollutants** — correlation heatmap and pollutant concentrations broken down by AQI category.
5. **RQ3 — Unhealthy** — ranking of cities by share of unhealthy days, plus a city × month heatmap of exposure.
6. **Findings** — a written summary with a data-quality caveat.

## Key findings

- **Air quality is strongly seasonal.** Average AQI peaks in winter (~221) and post-monsoon (~215), and is lowest during the monsoon (~116) when rain clears particulates. Day of week barely matters.
- **Particulate matter drives the index.** PM10 correlates most strongly with AQI (0.80), followed by CO and PM2.5 (~0.67); ozone is only weakly related (0.20). Pollutant levels rise step-by-step across the Good → Severe categories.
- **About 26% of all city-days are unhealthy** (AQI > 200), concentrated in northern cities. Ahmedabad (~82% of days) and Delhi (~65%) are worst hit, while coastal and southern cities such as Mumbai, Chennai, and Bengaluru stay below ~7%.
- **Data-quality note:** Ahmedabad shows some implausible AQI values (max ≈ 2049, well above the CPCB ceiling of 500), pointing to sensor/data-quality issues rather than real readings.

## How to run

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/aqi-analysis.git
   cd aqi-analysis
   ```
2. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn jupyter
   ```
3. Launch the notebook:
   ```bash
   jupyter notebook AQI_Analysis.ipynb
   ```
   Run the cells top to bottom. Figures are generated inline and the notebook reads the data from `data/city_day.csv`.

## Tech stack

Python · pandas · NumPy · matplotlib · seaborn · Jupyter

## Data source & attribution

Central Pollution Control Board (CPCB), via the Kaggle dataset *"Air Quality Data in India (2015–2020)."* Used here for educational, non-commercial analysis.

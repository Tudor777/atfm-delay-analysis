# European Airports ATFM Delay Analysis (2019–2026)

An analysis of Air Traffic Flow Management (ATFM) and Air Traffic Control (ATC) delays across Europe's busiest airports. This project investigates the root causes of flight delays and visualizes historical traffic trends.

## Core Objectives

1. Calculate the share of pre-departure delay caused by restrictions at the departure airport (IATA delay code 89, mainly local ATC).
2. Identify which airports are hit hardest by arrival ATFM delay, and when (season, weekday and traffic level).
3. Analyze whether ATFM slot adherence deteriorates during high-delay periods.
4. Forecast an airport's daily arrival ATFM delay one week ahead.

## Key Findings

1. **Code-89 (local ATC) delay is a small but growing part of pre-departure delay.** In 2025 it was 2–16% of total pre-departure delay at the 11 airports. It rose sharply at London Heathrow (4% in 2020 → 16% in 2025), Athens (4% → 14%) and Amsterdam (7% → 12%). Bucharest had the highest total pre-departure delay (19.8 min per departure in 2025), but only 2% of it was code 89.
2. **Athens ATH and Amsterdam AMS have the most arrival ATFM delay, for different reasons.** Athens (5.5 min per arrival in 2025) is almost entirely *ATC capacity* and peaks every spring (8.7 min in May). Amsterdam (4.8 min) splits between *weather* and *aerodrome capacity* and is high all year. Across the 11 airports, Friday and Monday are the worst days.
3. **Slot adherence drops on bad days because of late take-offs.** About 95–97% of regulated flights take off inside the −5/+10 min slot window. On each airport's worst-delay days, late take-offs more than double (0.9% → 2.2%), with the biggest drops at Rome (−4.3 points), Istanbul (−3.3) and Athens (−2.4).
4. **An exact delay cannot be forecast a week ahead, but vulnerable days can be identified.** A gradient boosting model did not beat the simple "average of the last 4 weeks" rule at predicting minutes of delay (mean absolute error 1.24 vs 1.20 min per arrival). A classifier for high-delay days (> 2 min per arrival) did better: 87% of the days it flagged were really high-delay days, against 81% for the 4-week average.

## Data Source

The data used in this project is provided by the [EUROCONTROL Aviation Intelligence Portal](https://ansperformance.eu/data/). It is used strictly for non-commercial, educational purposes in compliance with EUROCONTROL's copyright notice. The data is **not included** in this repository and must be downloaded from the portal (data downloaded in October 2026).

### Files to download

On [ansperformance.eu/data](https://ansperformance.eu/data/), download the **CSV version** of the following five datasets. Each dataset is split into one file per year; download every year listed below and **do not rename the files**.

| Dataset on the portal | File names | Years |
|---|---|---|
| Airport traffic | `airport_traffic_YYYY.csv` | 2016–2026 |
| Airport arrival ATFM delay (no post-ops) | `apt_dly_YYYY.csv.bz2` | 2014–2026 |
| All-causes pre-departure delay | `all_pre_departure_delays_YYYY.csv` | 2020–2026 |
| ATC pre-departure delay | `atc_pre_departure_delays_YYYY.csv` | 2019–2026 |
| ATFM slot adherence | `atfm_slot_adherence_YYYY.csv` | 2016–2026 |

The `.bz2` files are compressed CSVs. **Don't unzip them**: pandas reads them directly.

EUROCONTROL updates the current year's files regularly, so results for 2026 may differ slightly from those shown here.

### Folder structure

Put all downloaded files in a folder called `raw` inside `data_atfm`, next to the notebook:

```
atfm-delay-analysis/
├── analysis_atfm.ipynb
├── requirements.txt
└── data_atfm/
    ├── raw/              ← create this folder and put all 50 downloaded files here
    ├── processed/        ← created automatically: cleaned daily table
    └── images/           ← created automatically: charts
```

Only the `raw` folder has to be created by hand. The `processed` and `images` folders are created by the notebook when it runs.

## Tools Used

- Python (pandas, Matplotlib, scikit-learn)
- Jupyter Notebook / Google Colab

## How to Run

1. Clone or download this repository.
2. Install the libraries: `pip install -r requirements.txt`
3. Download the data files listed above and put them in `data_atfm/raw/`.
4. Open `analysis_atfm.ipynb` from the repository folder and run all cells. The first cell should print `data_atfm/raw -> 50 files found`.

## Limitations and Future Work

- The code-89 share combines two reporting sources (airlines and airport operators), so it is an estimate. Code 89 covers all restrictions at the departure airport, not only ATC.
- Daily airport totals can't show what happens to individual flights or rotations.




# From network to gate: ATFM and ATC delay at European airports (2019–2026)

An airport-level analysis of EUROCONTROL's public delay data for the 10 busiest European airports plus Bucharest Henri Coandă (LROP): how much departure delay is caused at the departure airport itself, which airports suffer most from arrival ATFM delay and when, whether flights keep to their ATFM slots on bad days, and whether delay can be forecast a week ahead.

**Tools:** Python, pandas, matplotlib, scikit-learn, Google Colab

![Arrival ATFM delay per arrival by cause, 2025](data_atfm/images/arrival_delay_by_cause_2025.png)

## Questions

1. What share of pre-departure delay is caused by restrictions at the departure airport (IATA code 89, mainly ATC)?
2. Which airports are hit hardest by arrival ATFM delay, and when (season, weekday, traffic level)?
3. Does ATFM slot adherence get worse when delay is high?
4. Can an airport's daily arrival ATFM delay be forecast one week ahead?

## Key findings

**1. Code-89 delay is a small but growing part of pre-departure delay.** In 2025 it was 2–16% of total pre-departure delay at the 11 airports. It rose sharply at London Heathrow (4% in 2020 → 16% in 2025), Athens (4% → 14%) and Amsterdam (7% → 12%). Bucharest has one of the highest total pre-departure delays (19.8 min per departure in 2025) but almost none of it is code 89 (2%).

![Share of pre-departure delay caused by code 89](data_atfm/images/atc_share_heatmap.png)

**2. Athens and Amsterdam have the most arrival ATFM delay, for different reasons.** Athens (5.5 min per arrival in 2025) is almost entirely *ATC capacity*, peaking every spring (8.7 min in May). Amsterdam (4.8 min) splits between *weather* and *aerodrome capacity* and is high all year. Each airport has its own season: Heathrow peaks in December, Barcelona in September. Across all 11 airports, Friday and Monday are the worst days, matching the busiest days of airline schedules.

![Arrival ATFM delay by month](data_atfm/images/seasonality_heatmap.png)

**3. Slot adherence drops on bad days because of late take-offs.** Overall about 95–97% of regulated flights take off inside the −5/+10 min slot window, even on days with the most regulations. But on each airport's worst-delay days, late take-offs more than double (0.9% → 2.2% of regulated flights). The drop is largest at Rome (−4.3 points), Istanbul (−3.3) and Athens (−2.4).

![Slot adherence on calm vs worst-delay days](data_atfm/images/slot_adherence_calm_vs_worst.png)

**4. Exact delay can't be forecast a week ahead, but risky days can be flagged.** A gradient boosting model did **not** beat the simple "average of the last 4 weeks" rule at predicting minutes of delay (MAE ≈ 1.2 min per arrival for both): the spikes come from weather and sudden capacity drops that past delay can't predict. A classifier for *high-delay days* (> 2 min per arrival) did better: 87% of the days it flagged were really high-delay days, against 81% for the 4-week average.

## Data

[EUROCONTROL Aviation Intelligence Portal](https://ansperformance.eu/data/), downloaded October 2026. Daily data per airport:

| Dataset | Period used |
|---|---|
| Airport traffic | 2016 – Aug 2026 |
| Airport arrival ATFM delay (by delay cause) | 2014 – Aug 2026 |
| All-causes pre-departure delay | 2020 – Aug 2026 |
| ATC pre-departure delay (IATA code 89) | 2019 – Aug 2026 |
| ATFM slot adherence | 2016 – Oct 2026 |

The raw files (~230 MB) are not in this repository. To rerun the notebook, download the CSV version of each yearly file of the five datasets above, without renaming them, and put them in `data_atfm/raw/`:

- `airport_traffic_YYYY.csv`
- `apt_dly_YYYY.csv.bz2`
- `all_pre_departure_delays_YYYY.csv`
- `atc_pre_departure_delays_YYYY.csv`
- `atfm_slot_adherence_YYYY.csv`

The cleaned daily table for the 11 airports (Jan 2019 – Aug 2026) is in `data_atfm/processed/daily_focus_airports.csv`.

## Method

- **Cleaning:** combined the yearly files; repaired a text-encoding error; unified two date formats; removed a duplicated row; checked what empty cells mean. In the arrival delay files an empty cell means *zero delay* (files up to 2022 leave it empty, later files write 0), so it was filled with 0. In the airport-reported columns an empty cell means *not reported*, so those days were left out.
- **Choosing a source:** the airport-reported code-89 delay is missing for Amsterdam, Heathrow and Istanbul, so the airline-reported version (CODA) was used.
- **Normalisation:** delay is expressed per flight (per arrival or per departure), using a ratio of sums, so airports of different sizes and busy vs quiet days are weighted correctly.
- **Forecast:** time-based split (train 2022–2024, test 2025 – Aug 2026); features use only information at least 7 days old; compared against simple baselines. An early version used the actual number of arrivals on the forecast day, which leaks information (cancellations on bad days), and was corrected.

## Limitations

- The code-89 share combines two reporting sources (airlines and airport operators), so it is an estimate.
- Code 89 covers all restrictions at the departure airport, not only ATC, so it is a proxy for ATC delay.
- Daily airport totals can't show what happens to individual flights or rotations.
- No weather data is used, which limits the forecast.

## Future work

- Add weather forecasts (TAF/METAR) to the forecast model.
- Use flight-level data (OPDI flight lists, EUROCONTROL research data) to follow delay along aircraft rotations.
- An interactive dashboard (Streamlit) to explore any airport.

## Repository structure

```
├── analysis_atfm.ipynb             ← the full analysis, with outputs
├── data_atfm/
│   ├── raw/                        ← put the downloaded EUROCONTROL files here (not included)
│   ├── processed/                  ← cleaned daily table for the 11 airports
│   └── images/                     ← charts used in this README
└── requirements.txt
```

## How to run

1. `pip install -r requirements.txt`
2. Download the raw files (see **Data**) into `data_atfm/raw/`.
3. Open `analysis_atfm.ipynb` from the repository folder and run all cells. The cleaned table and charts are written to `data_atfm/processed/` and `data_atfm/images/`.

## Author

Tudor — MSc student in Advanced Analytics for Business (UPB), BSc Aerospace Engineering, MCC engineer at an airline.

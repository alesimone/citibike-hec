# Citi Bike: which stations will run empty tomorrow morning?

HEC Paris Â· Master in Management Â· *Business Analytics Using Python* Â· Group 01 Â· October 2026

Citi Bike moves bikes by truck, mostly at night, so the list of stations to refill has to exist the evening before. This project predicts, from information available at night, which stations will be empty tomorrow morning (7â€“10am), and turns the prediction into an operating rule for the rebalancing teams.

## Main results

- **Predictable the evening before:** XGBoost reaches an AUC of 0.906 on 30,000 held-out station-days (random forest 0.893, logistic regression 0.865, decision tree 0.842).
- **Beats the obvious rule:** a list covering 10% of stations catches 62% of next-morning shortages, against 51% for "visit the stations empty longest today".
- **Commuter stations first:** residential stations are 28% of station-days but 43% of the drop-off list.
- **Trucks can also collect:** stations that are full tonight and low risk are natural pick-up points.
- **Stable over time:** AUC stays between 0.86 and 0.92 over the following year.

Full write-up in [`3_Report_and_presentation/CitiBike_Report_Group01.pdf`](3_Report_and_presentation/CitiBike_Report_Group01.pdf).

## Data

| Source | Size |
|---|---|
| Citi Bike System Data (trips) | 50.8 million rides |
| Station status feed | 1,063 million two-minute snapshots |
| Meteostat weather | daily |
| Apple App Store reviews | 500 |

These become 851,613 station-days across 2,318 stations. A shortage is a station empty for at least one hour between 7 and 10am (4.7% of station-days).

## Repository

| Folder | Content |
|---|---|
| `1_Data_cleaning/` | `01_Data_cleaning.ipynb` and the cleaned inputs: trips, station snapshots, weather, reviews |
| `2_Analysis/` | `02_Analysis.ipynb`, station-day table, cleaning logs and samples |
| `3_Report_and_presentation/` | final report, slides (PDF and PPTX), charts and `03_Charts_for_slides.ipynb` |

Run the notebooks in numeric order (`01`, `02`, `03`). Everything is in Python (pandas, scikit-learn, XGBoost, imbalanced-learn, mlxtend, gensim).

## Large files

Everything over 100 MB is stored with [Git LFS](https://git-lfs.com), so install it before cloning:

```bash
git lfs install
git clone https://github.com/alesimone/citibike-hec.git
```

To clone without the large files, use `GIT_LFS_SKIP_SMUDGE=1 git clone ...`, then `git lfs pull` when needed.

### Two files split in parts

GitHub LFS does not accept files above 2 GB, so these two files in `1_Data_cleaning/` are stored as parts of 2 GB:

| File | Parts | SHA-256 of the rebuilt file |
|---|---|---|
| `citi_bike_stations_2025.parquet` (4.3 GB) | `.part01` to `.part03` | `9663160362d1ccb94431f79134b2dd197116e4b8a5b0c7a20126a185dc859209` |
| `citi_bike_stations_2026.parquet` (3.2 GB) | `.part01` to `.part02` | `cd6ad64ea6ed15278c7ecc3857e6bde16417dc5a16f144c69450930e85fd5082` |

Rebuild them before running `01_Data_cleaning.ipynb`:

```bash
cd 1_Data_cleaning
cat citi_bike_stations_2025.parquet.part* > citi_bike_stations_2025.parquet
cat citi_bike_stations_2026.parquet.part* > citi_bike_stations_2026.parquet
```

On Windows (cmd):

```bat
cd 1_Data_cleaning
copy /b citi_bike_stations_2025.parquet.part01+citi_bike_stations_2025.parquet.part02+citi_bike_stations_2025.parquet.part03 citi_bike_stations_2025.parquet
copy /b citi_bike_stations_2026.parquet.part01+citi_bike_stations_2026.parquet.part02 citi_bike_stations_2026.parquet
```

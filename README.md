# How Effective Is the MLB ABS (Automated Ball-Strike) System?

A data analysis of the Automated Ball-Strike (ABS) Challenge System in Triple-A baseball, the minor league level where MLB tested the system before bringing it to the majors in 2026.

Under the challenge system, a batter, pitcher or catcher can challenge a ball or strike call, and Hawk-Eye tracking settles it. Each team has a limited number of challenges and keeps one when it wins. This project asks:

- How often do challenges overturn the umpire?
- Do batters and fielders (catchers and pitchers) challenge equally well?
- How much does success vary by team and player?

## Data

- **Source:** [Baseball Savant ABS Challenges leaderboard](https://baseballsavant.mlb.com/leaderboard/abs-challenges) (MLB / Statcast)
- **Filters:** Level = AAA, Year = 2025, Game Type = Regular Season, Batters tab, exported with **Download CSV** as `abs-challenges-2025-batter.csv`
- **Shape:** 667 rows, one per batter per team. Each row has challenges the batter made (`n_challenges`, `n_overturns`) and challenges the fielding team made while that batter was at the plate (the `_against` columns).

The raw file is not included in this repository. Download it from Baseball Savant with the filters above.

## Method

The notebook `abs_data_collection_cleaning.ipynb` does the following:

1. **Load** the CSV in Python (pandas) on Google Colab.
2. **Verify and clean:** confirm the file is Triple-A 2025, drop empty and duplicate rows, check that overturns plus confirms equal challenges, and check Savant's overturn rate against the raw counts (all rows matched).
3. **Reshape** the wide file into a tidy table with one row per batter and role (`batter` or `fielder`), dropping rows with zero challenges.
4. **Query with SQL** (SQLite): total challenges and overturns by role and team, a leaderboard limited to batters with at least 10 challenges, and a look at how small samples distort rates.
5. **Check** the results against figures MLB.com published for the same season.

Overturn rates are computed as total overturns divided by total challenges, not as an average of per-batter rates, which would overweight batters with only a few challenges.

## Results

| Who challenged | Challenges | Overturned | Overturn rate |
|---|---|---|---|
| Batters | 4,487 | 2,022 | 45.1% |
| Fielders | 4,559 | 2,449 | 53.7% |
| All | 9,046 | 4,471 | 49.4% |

- Fielders (catchers and pitchers combined) won about 9 percentage points more often than batters.
- Among Triple-A teams, batting challenge success ranged from 36.6% (Louisville) to 55.0% (Oklahoma City).
- Rates for batters with fewer than 10 challenges ranged from 0% to 100%, so the notebook ranks only batters with at least 10.
- These totals line up with MLB.com's published analysis of the same season (about 45% for batters, nearly 55% for fielders and about 50% overall).


## Limitations

- **An overturn rate is not an umpire accuracy rate.** Players choose which pitches to challenge, so the rate describes challenged pitches only.
- **Triple-A is not MLB.** Minor league players and umpires differ from major leaguers, so results may not carry over exactly.
- **"Fielder" combines catchers and pitchers.** The file does not separate them. In the `fielder` rows, the team column is the batter's team, not the fielding team, so team-level results use batters only.
- **Total challenges differ from MLB.com's count.** This file has 9,046 challenges, while MLB.com's article reports 9,432. I have not determined why; it may come from a data refresh or a filter difference.

## Repository contents

| File | Description |
|---|---|
| `abs_data_collection_cleaning.ipynb` | Collection notes, cleaning, SQL queries, sanity checks and chart, with outputs |
| `data/clean/abs_challenges_aaa_2025_clean.csv` | Cleaned, tidy table produced by the notebook |
| `images/overturn_by_role.png` | Chart used on the portfolio site |

## Reproduce

1. Download the CSV from Baseball Savant using the filters above.
2. Open `abs_data_collection_cleaning.ipynb` in Google Colab.
3. Run all cells and upload the CSV when prompted.

Requires Python with pandas, NumPy and Matplotlib (all preinstalled in Colab). SQLite ships with Python.

## Sources

- [MLB.com: There's loads of ABS data from last season. What does it all mean?](https://www.mlb.com/news/abs-challenge-system-statistical-breakdown-2025)
- [MLB.com: ABS Challenge System rules and explainer](https://www.mlb.com/news/abs-challenge-system-mlb-2026)
- [Baseball Savant: ABS Dashboard](https://baseballsavant.mlb.com/abs)

## Author

Jayden Ko, UCLA, B.S. Statistics & Data Science ([LinkedIn](https://www.linkedin.com/in/ko-jayden/))

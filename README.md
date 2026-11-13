# Where You Grow Up Matters
### Predicting Adult Income for Low-Income Children in the DC Region

**Springboard Data Science Career Track: Capstone Two**

## Project Question
**Which neighborhood characteristics best predict adult income for children raised in low-income families in the DC metro area?**

Children who start out in similar low-income families can end up with very different adult incomes depending on the neighborhood they grow up in. In this data, average household income at age 35 for children from low-income families ranges from about **$19,500 to $69,200** across DC-area Census tracts. This project looks at which neighborhood features, such as poverty rate, single-parent share, rent and population density, explain that gap.

## Data Source
All data comes from **[The Opportunity Atlas](https://www.opportunityatlas.org/?%7B%22mapping%22%3A%22kfr%22%2C%22dataToggles%22%3A%7B%22parentIncome%22%3A%22p25%22%2C%22childRace%22%3A%22rP%22%2C%22childGender%22%3A%22gP%22%2C%22cohortTimeline%22%3A%22e%22%7D%2C%22compareDataToggles%22%3A%7B%22parentIncome%22%3A%22pall%22%2C%22childRace%22%3A%22rP%22%2C%22childGender%22%3A%22gP%22%2C%22cohortTimeline%22%3A%22e%22%7D%2C%22selectedChapterId%22%3A%22chapter1%22%2C%22compareFeatures%22%3A%22loc%22%2C%22selectedFeatureByID%22%3A11001010200%2C%22filters%22%3A%5B%5D%2C%22mode%22%3A%22standardMode%22%2C%22mapBounds%22%3A%5B%5B-77.29824387656735%2C38.72686964592006%5D%2C%5B-76.79125493105334%2C39.0687791383437%5D%5D%2C%22floatingPanelIsMinimized%22%3Afalse%2C%22showStoryMarkers%22%3Atrue%2C%22showHistogram%22%3Afalse%2C%22propertyShownOnMap%22%3A%22kfr_rP_gP_p25_e%22%7D)** (Opportunity Insights and the U.S. Census Bureau), downloaded for the DC region map view.

| File | Contents | Role |
|---|---|---|
| `shown_tract_kfr_rP_gP_p25.csv` | Average household income at age 35 for children whose parents were at the 25th income percentile (all races and genders), by Census tract | **Target** |
| `shown_tract_allCharacteristics.csv` | Neighborhood characteristics by Census tract | **Predictors** |

**Coverage:** Census tracts in Washington DC; Montgomery, Prince George's and Anne Arundel counties in Maryland; and Arlington and Fairfax counties and the cities of Alexandria, Fairfax and Falls Church in Virginia.

## Repository Structure
```
├── data/
│   ├── raw/                         # original Opportunity Atlas downloads (unedited)
│   │   ├── shown_tract_kfr_rP_gP_p25.csv
│   │   └── shown_tract_allCharacteristics.csv
│   └── processed/                   # files created by the notebooks
│       ├── dc_tracts_clean.csv
│       └── data_dictionary.csv
├── notebooks/
│   └── Capstone_Two_Data_Wrangling.ipynb
├── figures/                         # charts for the final report
└── README.md
```

## Project Steps
| Step | Notebook | Status |
|---|---|---|
| 1. Data Wrangling | `Capstone_Two_Data_Wrangling.ipynb` | ✅ Complete |
| 2. Exploratory Data Analysis | coming next | ⏳ In progress |
| 3. Pre-processing and Training Data Development | | ⏳ |
| 4. Modeling | | ⏳ |
| 5. Final Report and Presentation | | ⏳ |

## Step 1: Data Wrangling
**Data Collection**
- Loaded both files with `pandas.read_csv()`, using `glob` to locate them.
- Joined them with a one-to-one inner **merge** on the tract ID. All 756 outcome tracts matched. The 4 unmatched tracts were uninhabited special land-use tracts (such as parkland and military land).

**Data Organization**
- Raw and processed data are kept in separate folders, so the original downloads are never overwritten.

**Data Definition**
- Renamed the long original column names to short `snake_case` names.
- Stored `tract` as a text ID and created `state` and `county` columns from the tract name and ID.
- Built a data dictionary with each column's description, data type, unique counts and percents, and value ranges (`data/processed/data_dictionary.csv`).
- Found no impossible values. Noted that rent and household income are capped (top-coded) by the Census in the most expensive tracts.

**Data Cleaning**
| Issue | Decision | Reason |
|---|---|---|
| `job_growth` missing for 24% of tracts | Dropped the column | Missing for **100% of DC tracts**, so filling or dropping rows would remove or invent all DC data |
| Target missing for 4 tracts | Dropped the 4 rows | The target can't be filled in without making up the answer |
| 13 small gaps in `median_rent` and `frac_single_parents` | Filled with the county median | Under 2% of rows. The median resists skew, and the county median reflects local prices |
| Duplicates | None found | Each tract appears exactly once |

**Result:** `dc_tracts_clean.csv`, **752 tracts × 17 columns, no missing values** (1 target, 12 neighborhood features, plus tract, name, state and county).

## Features
| Column | Description |
|---|---|
| `income_age35` | **Target:** average household income at age 35 ($) for children from low-income families |
| `median_rent` | Median gross rent ($/month), 2012-16 |
| `median_hh_income_2016` | Median household income of residents ($), 2012-16 |
| `median_hh_income_1990` | Median household income of residents ($), 1990 |
| `poverty_rate` | Share of residents below the poverty line, 2012-16 |
| `frac_college_grad` | Share of adults with a college degree, 2012-16 |
| `frac_nonwhite` | Share of residents who are non-white, 2010 |
| `frac_foreign_born` | Share of residents born outside the U.S., 2012-16 |
| `frac_single_parents` | Share of households with children headed by a single parent, 2012-16 |
| `pop_density` | Residents per square mile, 2010 |
| `job_density` | Jobs per square mile, 2013 |
| `frac_short_commute` | Share of workers with a commute under 15 minutes, 2012-16 |
| `census_response_rate` | Census mail-in response rate (%), a proxy for social capital |

**Note on timing:** the children in this data grew up mostly in the 1980s and 1990s, while most neighborhood characteristics were measured in 2010-2016. `median_hh_income_1990` is the measure closest to their childhood.

## How to Run
1. Open the notebook in [Google Colab](https://colab.research.google.com/) (**File → Upload notebook**, or open it from this repository).
2. Run the first cells to create the folders.
3. Upload the two CSV files into `data/raw/`.
4. Choose **Runtime → Run all**.

**Requirements:** Python 3, `pandas`, `numpy`

## Author
Brittany, Springboard Data Science Career Track

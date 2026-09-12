# Morocco Food Price Inflation & Purchasing Power Analysis

An end-to-end data analytics project scraping official Moroccan consumer price data
to analyze food inflation trends and household purchasing power from 2009 to 2026.

## Project Status

- [x] Data collection — web scraping HCP's monthly CPI bulletins (245 months, 2009–2026)
- [x] Data quality & cleaning
- [x] Exploratory analysis
- [ ] World Bank wage/CPI comparison layer
- [ ] Power BI dashboard

## Overview

Morocco's Haut-Commissariat au Plan (HCP) publishes monthly Consumer Price Index (CPI)
bulletins, but the data is only available as human-readable Word/PDF/RTF documents —
not as a clean, analysis-ready dataset. This project builds a full pipeline to:

1. Scrape and download 17 years of official monthly CPI bulletins
2. Extract structured tables from three different file formats (.docx, .pdf, .rtf)
   across multiple site-structure eras
3. Clean and validate the resulting dataset (~1,500+ rows per table)
4. (Upcoming) Combine with World Bank wage data to analyze real purchasing power —
   are food prices rising faster than incomes?

## Data Sources

- **HCP (Haut-Commissariat au Plan)** — Morocco's official statistics agency.
  Monthly IPC (Indice des Prix à la Consommation) bulletins, scraped directly
  from [hcp.ma](https://www.hcp.ma).
- **World Bank API** — CPI, wage-and-salary-worker share, and unemployment
  indicators for Morocco and regional comparison countries (planned).

## Repository Structure

```
morocco-purchasing-power/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   ├── 01_download_data.ipynb      # Scraping + extraction pipeline
│   └── 02_data_quality.ipynb       # Cleaning, validation, normalization
└── data/
    └── processed/
        ├── hcp_monthly_category_clean.csv
        ├── hcp_yearly_category_clean.csv
        └── hcp_city_indices_clean.csv
```

## Datasets

| File | Description | Rows |
|---|---|---|
| `hcp_monthly_category_clean.csv` | Month-over-month CPI by product category (15 categories) | 1,582 |
| `hcp_yearly_category_clean.csv` | Year-over-year + cumulative CPI by product category | 1,757 |
| `hcp_city_indices_clean.csv` | CPI by city (up to 20 cities monitored) | 2,235 |

## Known Limitations

- **4 months (2011–2012)** could not be downloaded due to broken or unrecognized
  attachment links on HCP's archive.
- **7 additional months** (scattered 2010–2013, plus Nov 2022) only have the
  aggregate "Ensemble" index — individual category/city breakdowns were lost due
  to PDF table-extraction failures in the source documents for these bulletins.
- **Base-year rebasing**: the IPC series switched from base 100:2006 to base
  100:2017 around April 2020. Raw index values are **not directly comparable**
  across this boundary without rebasing — this will be addressed in the analysis
  phase.
- **"Errachidia"** appears only from ~2020 onward, reflecting HCP's expansion of
  monitored cities over time — not a data quality issue.

Overall coverage: ~230 of 241 target months (95%+) with complete category-level
detail.

## Tech Stack

- **Python** — `requests`, `BeautifulSoup4` (scraping), `python-docx`, `pdfplumber`
  (table extraction from .docx/.pdf), custom RTF parser (regex-based)
- **pandas** — data cleaning, transformation, validation
- **SQL** *(planned)* — structured querying layer
- **Power BI** *(planned)* — dashboard and visualization

## Setup

```bash
pip install -r requirements.txt
```

Run the notebooks in order:
1. `01_download_data.ipynb` — scrapes HCP's site and builds raw datasets
   (takes ~20-30 minutes due to polite rate-limiting)
2. `02_data_quality.ipynb` — cleans and validates, outputs final CSVs to
   `data/processed/`

## Data Pipeline Highlights

This project deals with real-world messiness that's often missing from
tutorial datasets:

- **Three file formats** across the archive's history (.docx, .pdf, .rtf),
  each requiring different table-extraction logic
- **Inconsistent HTML/URL structure** across ~17 years of site redesigns
- **Character encoding drift** (curly vs. straight apostrophes, French accent
  variants) breaking naive regex parsing
- **Category/city name inconsistencies** (spelling, punctuation, casing) across
  years, requiring canonical-name normalization
- **Header rows leaking into data** and requiring position/content-based
  detection to filter out

## License

Data sourced from HCP (public government statistics) and the World Bank
(open data). Code in this repository is available under the MIT License.
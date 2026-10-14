# Beijing-Hangzhou Grand Canal analysis code

This repository provides analysis and plotting code for **Adaptive maintenance
helped sustain the Beijing-Hangzhou Grand Canal through five centuries of
hydroclimatic extremes**. It covers Figures 2-6, Figure S1, Table 2, and Tables
S1-S9.

## Quick start

Run the following commands from the repository's root directory. Use a separate
Python 3.12 virtual environment. The workflow was tested with Python 3.12.14 and
the package versions listed in `requirements.txt`.

Create the environment:

```text
python -m venv .venv
```

Activate it in Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Or activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```text
python -m pip install -r requirements.txt
```

Place the six input Excel files listed under **Main input files** in `data/`,
alongside the two CSV input tables included in this repository. Then run:

```text
python run_all.py
```

Figures, numerical tables, and a verification report are written to `results/`.
The workflow reads the input files without modifying them.

To use a separate directory containing the six Excel files:

```text
python run_all.py --data-dir ../Grand_Canal_research_data --output-dir results
```

The two additional CSV inputs are read from the selected data directory if
present, otherwise from this repository's `data/` directory.

## Scripts and outputs

| Script | Analysis | Main output |
|---|---|---|
| `classify_extremes.py` | Regional extreme-year identification, criterion agreement, and uniform-criterion sensitivity | Tables S2-S4 |
| `figure2.py` | Decadal extreme-event counts, Sen slopes, Mann-Kendall tests, dynastic frequency, and consecutive-year occurrence | Figure 2, Table 2 |
| `autocorrelation.py` | Linear detrending, residual lag-1 autocorrelation, and Ljung-Box tests across lags 1-5 | Table S1 |
| `figure3.py` | Annual grain-transport records, pressure classes, decadal medians, and within-decade variability | Figure 3 |
| `figure4.py` | Maintenance types, reach distribution, and decadal counts | Figure 4 |
| `figure5.py` | Annual PPML models, Holm adjustment, sensitivity analyses, and descriptive decadal Poisson regression | Figure 5, Tables S5-S6 |
| `figure6.py` | Huanghuai pressure and maintenance compared with whole-corridor grain-transport records | Figure 6 |
| `station_availability.py` | Annual availability of selected regional station grades | Figure S1 |
| `source_coverage.py` | Source composition, matched-decade comparisons, and source-restricted models | Tables S7-S9 |
| `canal_common.py` | Shared input readers, path settings, and figure-export options | Used by the analysis scripts |
| `run_all.py` | Execute the complete workflow and verify numerical results | All analyses and `verification.json` |
| `verify_results.py` | Compare numerical outputs with values reported in the manuscript | `verification.json` |

Analysis scripts can also be run individually. For example:

```text
python figure5.py --data-dir data --output-dir results/figure5 --formats png pdf --dpi 300
```

Supported figure formats are `png`, `pdf`, `svg`, and `tiff`; the defaults are
PNG and PDF. Use `--dpi 600` for high-resolution raster exports. TIFF exports use
LZW compression. Figures use Arial where available; Matplotlib substitutes an
available font on systems without Arial.

## Main input files

The complete workflow requires these six Excel files:

| File | Structure and interpretation |
|---|---|
| `HEBEI_DRY_WET_TABLE.xlsx` | Headerless Year and Classification columns; 544 rows, 1368-1911 |
| `HUANGHUAI_DRY_WET_TABLE.xlsx` | Same structure for Huanghuai |
| `HUAINAN_DRY_WET_TABLE.xlsx` | Same structure for Huainan |
| `caoyun_ming.xlsx` | Headerless Year and Volume columns; 122 observations, 1411-1626 |
| `caoyun_qing.xlsx` | Headerless Year and Volume columns; 50 observations, 1724-1849 |
| `coded_maintenance_events.xlsx` | `events` worksheet with an English header row and 290 reach-level interventions |

Regional classifications are -1 for an identified extreme drought year, +1 for
an identified extreme flood year, and 0 for a year not identified as either
extreme. Each regional file must contain all study years. The reader preserves
the first numeric row and also accepts an explicit English Year header.

Grain-transport volumes are expressed in **10^4 dan**, the historical grain unit
retained in the study. Missing transport years remain missing; they are neither
interpolated nor assigned zero values.

The maintenance worksheet contains the following fields:

| Field | Meaning |
|---|---|
| `Event_ID` | Unique reach-level intervention identifier |
| `Year` | Year assigned to the intervention for analysis |
| `Dynasty` | Ming or Qing, with 1644 assigned to Qing |
| `Reach` | Engineering reach associated with the intervention |
| `Region` | Hebei, Huanghuai, or Huainan |
| `Adaptive_type` | Reactive restoration, Proactive adjustment, or Mixed |
| `Implementation_evidence` | Implemented or Ordered implementation |
| `Has_reactive` | Yes or No; Mixed interventions have Yes |
| `Has_proactive` | Yes or No; Mixed interventions have Yes |
| `Yellow_River_context` | No, Direct, Mixed, or Unclear |

The implementation-only analysis selects records with
`Implementation_evidence == "Implemented"`.

## Additional input tables

Two CSV tables are included in `data/` and are required for the supplementary
analyses.

### `station_grades.csv`

This table contains 10,880 site-year rows for 20 selected sites over 1368-1911,
with columns `Region`, `Year`, `Site`, and `Grade`. Site names are in English.
The table was extracted from the study's compiled regional grade sheets.

Grade codes are 0 for unavailable, 1 for severe dry conditions, 2 for dry
conditions, 3 for normal conditions, 4 for wet conditions, and 5 for severe wet
conditions. Availability counts include grades 1-5 and describe the compiled
station-grade series.

### `maintenance_source_groups.csv`

This table links all 290 intervention IDs to the source categories used in
Tables S7-S9. The categories were derived from the citations supporting each
intervention in the coded event database.

| Field | Meaning |
|---|---|
| `Event_ID` | Identifier linking to the maintenance worksheet |
| `Contemporary_official` | At least one cited court chronicle or archival/administrative source |
| `Later_central` | At least one cited dynastic history or government river-regulation compilation |
| `Gazetteer` | At least one cited provincial or local gazetteer |
| `Central_attested` | True when either Contemporary_official or Later_central is True |
| `Source_group` | Central only, Central and gazetteer, Gazetteer only, or Other only |

The four source groups are mutually exclusive: 122 Central only, 72 Central and
gazetteer, 95 Gazetteer only, and 1 Other only. Central attestation therefore
covers 194 interventions. Other only denotes support from privately authored
historical writing. These categories describe citations supporting included
interventions, not the volume of surviving documents.

## Analysis conventions

- The analysis period is 1368-1911. Ming is 1368-1643 and Qing is 1644-1911.
- Trend tests and pressure thresholds use 54 complete calendar decades from
  1370-1379 through 1900-1909. Partial boundary decades are displayed where
  relevant but excluded from these calculations.
- Whole-corridor extreme-event counts sum the three regional series. A year
  affecting two regions contributes two region-year records.
- Hydroclimatic pressure is the decadal sum of identified extreme flood and
  drought years. Tercile thresholds are calculated for the spatial unit being
  compared. Low pressure is at or below the lower threshold; moderate pressure
  is above the lower threshold and at or below the upper threshold; high
  pressure is above the upper threshold. Ties can produce unequal class sizes.
  Figure 3 uses thresholds of 4 and 6 records.
- Figure 3 includes decadal medians for decades with at least one transport
  observation. A one-record decade uses that observed value. The coefficient
  of variation uses the sample standard deviation and requires at least three
  observations. Lines connect only consecutive observed years. Group
  interquartile ranges are displayed for groups with at least three eligible
  decades.
- Table 2 defines N1 as starting-year event records with a following year in
  the same dynasty, N11 as pairs of the same extreme in the same region in
  consecutive years, and P11 as N11/N1. Whole-corridor counts pool regional
  records and pairs. Cross-dynasty pairs are excluded.
- Figure 5 primary models jointly include current-year and preceding-year
  flood and drought variables, calendar-decade fixed effects, and standard
  errors clustered by decade. Lags are constructed within each dynasty;
  1368 and 1644 are excluded because no eligible preceding-year value exists.
  Decades without any outcome event are omitted in the fixed-effect models.
- Holm adjustment is applied across the 16 primary coefficients. The
  source-restricted models in Table S9 form a separate 16-coefficient family.
  Alternative specifications in Table S6 and matched-support comparisons in
  Table S8 are exploratory and use unadjusted P values.
- The spline alternative retains zero-intervention decades, includes a cubic
  spline with six degrees of freedom and a Qing-period term, and uses
  heteroskedasticity- and autocorrelation-consistent (HAC) standard errors with
  a maximum lag of 10 years. The descriptive decadal Poisson model uses HAC
  standard errors with a maximum lag of one decade.
- Mixed interventions count once in the total outcome and once in each of the
  two separate component-specific outcomes. These overlapping component counts
  are not summed.
- Figure 6 uses Huanghuai pressure and maintenance, standardized separately
  using sample standard deviations. High pressure and high maintenance exceed
  their respective upper-tercile thresholds; low maintenance is at or below
  its lower-tercile threshold. Thresholds in the unstandardized counts are 2
  pressure records, 5 maintenance interventions for the upper threshold, and
  2 maintenance interventions for the lower threshold. Qualifying decades
  are selected independently of transport availability and values.
- Same-year models describe annual co-occurrence. Regional extreme-year
  classifications characterize regional conditions and do not locate flooding
  at individual canal reaches.

## Reproducibility check

The complete workflow was tested using the study input datasets. The generated
numerical results agreed with the values reported in the manuscript at the
reported precision.

To check the numerical outputs after running the analyses:

```text
python verify_results.py --output-dir results
```

Expected values are stored in `expected_tables.json` and
`expected_main_table2.json`. The validation results and tested package versions
are documented in `validation_report.json`. Running the verification script
also writes `verification.json` to the selected output directory.

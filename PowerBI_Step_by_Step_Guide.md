# How to Build This Project in Power BI — Step by Step

This guide walks you from the raw WHO dataset to a finished, interactive Power BI dashboard that answers all twelve business questions, using a clean **star schema** and proper **KPIs (death rate, survival rate, YoY growth)**.

> **What you need:** Power BI Desktop (free — download from the Microsoft Store or powerbi.microsoft.com) and the file `HIV_dataset.xlsx` (or the Google Sheet link from the brief).

---

## Stage 0 — Understand the data first

The raw table is in **long format**: one row per `indicator × country × year`.

| Column | Meaning |
|---|---|
| `indicator` | One of: New infections, HIV deaths, People living with HIV, ART coverage (%) |
| `location_code` | WHO region code (AFR, AMR, EMR, EUR, SEAR, WPR) |
| `continent` | WHO region name |
| `country` | 150 countries |
| `year` | 2000–2024 |
| `value` | A **count** (infections/deaths/PLHIV) **or a percentage** (ART coverage) |

**Two things to remember throughout:**
1. ART coverage is a **percentage** → never SUM it, always AVERAGE it.
2. `New infections` and `ART coverage` are only reported in 2000, 2005, 2010, 2015, then yearly 2020–2024. Don't treat missing years as zero in trend visuals.

---

## Stage 1 — Get the data

1. Open **Power BI Desktop** → **Home → Get Data → Excel workbook** (or **Web** if using the Google Sheet — publish it to CSV first, or download as .xlsx).
2. Select `HIV_dataset.xlsx`, check the `HIV_dataset` sheet in the Navigator, then click **Transform Data** (not "Load") to open **Power Query Editor**.

---

## Stage 2 — Clean the data (Power Query)

In Power Query Editor, rename the query to `Source_HIV` and apply these steps:

1. **Promote headers** if needed (Home → Use First Row as Headers).
2. **Set data types** (click each column's type icon):
   - `indicator`, `location_code`, `continent`, `country` → **Text**
   - `year` → **Whole Number**
   - `value` → **Whole Number** (or Decimal)
3. **Trim & clean text** — select `country`, `indicator` → **Transform → Format → Trim**, then **Clean** (removes stray spaces / non-printing characters that break relationships).
4. **Remove errors / blank rows** — Home → Remove Rows → **Remove Errors** and **Remove Blank Rows**.
5. **Remove duplicates** — select all columns → **Remove Rows → Remove Duplicates**.
6. **Standardise region names** (optional but tidy) — confirm the six regions are spelled consistently.

Leave this `Source_HIV` query as your single clean source. We'll branch the dimension and fact tables from it by **referencing** it.

---

## Stage 3 — Build the Star Schema

You'll create **1 fact table + 3 dimension tables**. The cleanest way in Power Query is to **Reference** `Source_HIV` for each table.

### 3.1 Dim_Geography
1. Right-click `Source_HIV` → **Reference**. Rename to `Dim_Geography`.
2. Keep only `country`, `location_code`, `continent` → **Home → Choose Columns**.
3. **Remove Duplicates** (Home → Remove Rows). You should get **150 rows**.
4. Rename columns: `country → Country`, `location_code → RegionCode`, `continent → WHO_Region`.
5. Add an index key: **Add Column → Index Column → From 1**, rename to `CountryKey`.

### 3.2 Dim_Indicator
1. Reference `Source_HIV` → rename `Dim_Indicator`.
2. Keep only `indicator` → Remove Duplicates → **4 rows**.
3. Rename `indicator → Indicator`.
4. **Add Column → Conditional Column** `Measure_Type`:
   - New infections → `Incidence`
   - HIV deaths → `Mortality`
   - People living with HIV → `Prevalence`
   - ART coverage (%) → `Treatment Access`
5. **Add Column → Conditional Column** `Unit`: ART coverage (%) → `Percentage`, else → `Count`.
6. Add **Index Column → From 1** → rename `IndicatorKey`.

### 3.3 Dim_Date
1. Reference `Source_HIV` → rename `Dim_Date`.
2. Keep only `year` → Remove Duplicates → **25 rows** → rename `year → Year`.
3. **Add Column → Custom Column** `Decade` = `Number.RoundDown([Year]/10)*10` then format as text + "s".
4. **Add Column → Conditional Column** `ART_Era`: `Year < 2005` → "Pre-ART", `Year < 2015` → "Early ART", else "Modern ART".
5. Set `Year` as the key (it's already unique). Optionally duplicate to `DateKey`.

### 3.4 Fact_HIV
1. Reference `Source_HIV` → rename `Fact_HIV`.
2. **Merge** the keys so the fact only stores keys + the measure:
   - **Home → Merge Queries** → join `Fact_HIV` with `Dim_Geography` on `country = Country` → expand only `CountryKey`.
   - Merge with `Dim_Indicator` on `indicator = Indicator` → expand `IndicatorKey`.
3. Keep columns: `IndicatorKey`, `CountryKey`, `year` (rename `DateKey`), `value` (rename `Value`).
4. (Optional) Add **Index Column → From 1** → `FactID`.

> 💡 Shortcut: the included file **`HIV_Star_Schema_Model.xlsx`** already contains all four tables (Fact_HIV, Dim_Geography, Dim_Indicator, Dim_Date) plus a data dictionary — you can load it directly and skip Stage 3.

5. **Close & Apply** (Home → Close & Apply) to load all four tables.

---

## Stage 4 — Create relationships (Model view)

Open **Model view** (left sidebar) and create these one-to-many relationships (drag key → key):

| From (one) | To (many) | Keys |
|---|---|---|
| `Dim_Geography[CountryKey]` | `Fact_HIV[CountryKey]` | 1 → * |
| `Dim_Indicator[IndicatorKey]` | `Fact_HIV[IndicatorKey]` | 1 → * |
| `Dim_Date[Year]` | `Fact_HIV[DateKey]` | 1 → * |

Set **cross-filter direction = Single** (dimension filters fact). The layout should look like a star: `Fact_HIV` in the centre, three dimensions around it. ⭐

---

## Stage 5 — Write the DAX measures

Create a measures table for tidiness: **Home → Enter Data** → make an empty table `_Measures`, then add measures into it. Copy these in (New Measure):

```DAX
-- Base totals (filtered by indicator via the dimension)
New Infections =
CALCULATE ( SUM ( Fact_HIV[Value] ),
    Dim_Indicator[Indicator] = "New infections" )

HIV Deaths =
CALCULATE ( SUM ( Fact_HIV[Value] ),
    Dim_Indicator[Indicator] = "HIV deaths" )

People Living with HIV =
CALCULATE ( SUM ( Fact_HIV[Value] ),
    Dim_Indicator[Indicator] = "People living with HIV" )

Avg ART Coverage =
CALCULATE ( AVERAGE ( Fact_HIV[Value] ),
    Dim_Indicator[Indicator] = "ART coverage (%)" )
```

```DAX
-- KPIs
Death Rate % =
DIVIDE ( [HIV Deaths], [People Living with HIV] )

Survival Rate % =
1 - [Death Rate %]

Deaths per 1000 Infections =
DIVIDE ( [HIV Deaths], [New Infections] ) * 1000
```

```DAX
-- Year-over-year growth in new infections
Infections YoY % =
VAR CurrYear = MAX ( Dim_Date[Year] )
VAR Curr = [New Infections]
VAR Prev =
    CALCULATE ( [New Infections],
        Dim_Date[Year] = CurrYear - 1 )
RETURN DIVIDE ( Curr - Prev, Prev )
```

```DAX
-- PLHIV-weighted ART coverage (the honest "global" coverage number)
ART Coverage (Weighted) =
VAR t =
    ADDCOLUMNS (
        VALUES ( Dim_Geography[Country] ),
        "@art", [Avg ART Coverage],
        "@p", [People Living with HIV]
    )
RETURN DIVIDE ( SUMX ( t, [@art] * [@p] ), SUMX ( t, [@p] ) )
```

Format `Death Rate %`, `Survival Rate %`, `Infections YoY %` as **Percentage**; counts as **Whole Number** with thousands separators.

---

## Stage 6 — Build the dashboard

Aim for a **single clean page** (or two). Suggested layout, top to bottom:

### Row 1 — KPI cards (use the **Card** visual)
- New Infections, HIV Deaths, People Living with HIV, Avg ART Coverage, Death Rate %, Survival Rate %.

### Row 2 — Trends (answers Q1, Q2, Q7)
- **Line chart**: Axis = `Dim_Date[Year]`, Values = `New Infections` + `HIV Deaths` (compares the two trends).
- **Area/line chart**: Axis = `Year`, Value = `People Living with HIV` (rising burden).

### Row 3 — Treatment & geography (answers Q4, Q8, Q11)
- **Bar chart**: `Avg ART Coverage` by `Dim_Date[Year]` (treatment scale-up).
- **Map** or **clustered bar**: `People Living with HIV` by `WHO_Region` (most-affected regions).
- **Scatter chart** (the correlation): X = `Avg ART Coverage`, Y = `Death Rate %`, Details = `Country`. Add a **trend line** (Analytics pane) to show the negative relationship.

### Row 4 — Risk & ranking (answers Q6, Q9, Q10, Q12)
- **Top-N bar**: `People Living with HIV` by `Country`, filtered Top 10 (Filters pane → Top N).
- **Bar**: `Deaths per 1000 Infections` by `Country`, Top 10.
- **Table**: `Country`, `People Living with HIV`, `Avg ART Coverage`, `Death Rate %` — sort by ART ascending to surface at-risk countries; add conditional formatting (red < 50%).

### Map tip
Use the **Filled Map** with `Country` for the geographic location field; set the legend/colour to `Death Rate %` or `People Living with HIV`.

---

## Stage 7 — Add interactive slicers

Add **Slicer** visuals (required by the brief):
- `Dim_Date[Year]` — as a **between** slider.
- `Dim_Geography[WHO_Region]` — dropdown.
- `Dim_Geography[Country]` — searchable list.
- `Dim_Indicator[Indicator]` — buttons (optional).

Use **Format → Edit interactions** to control how each slicer filters each visual.

---

## Stage 8 — Polish & publish

1. **Theme**: View → Themes → Customize. Use a navy + orange palette to match the brief (navy `#1F3A5F`, orange `#E07B1C`, teal `#2A9D8F`).
2. Add a **title** ("Global HIV Trends & Treatment Effectiveness, 2000–2024") and a small "Data: WHO" footnote.
3. Align visuals with gridlines; keep ≥ 0.5 cm margins; don't overcrowd.
4. **File → Save** as `HIV_Dashboard.pbix`.
5. **Submission**: take screenshots of your key charts and drop them into the presentation slides, then **export the slides to PDF**. (The included `HIV_Presentation.pdf` already follows this storytelling structure as a model.)
6. Publish to the Power BI Service (optional) for a shareable link, then post on LinkedIn and tag **10Alytics**. 🎉

---

## Mapping: business question → visual

| # | Business Question | Visual / Measure |
|---|---|---|
| 1 | New infections over time | Line chart (Year vs New Infections) |
| 2 | Total HIV deaths | Card + line chart |
| 3 | People living with HIV | Card + area chart |
| 4 | Overall treatment coverage | Card `Avg ART Coverage` + bar by year |
| 5 | Mortality & survival rate | Cards `Death Rate %`, `Survival Rate %` |
| 6 | Deaths per 1,000 infections (worst countries) | Top-N bar `Deaths per 1000 Infections` |
| 7 | Infections vs deaths trend | Two-series line chart |
| 8 | Most affected regions | Bar/map by `WHO_Region` |
| 9 | Best treatment access | Top-N bar `Avg ART Coverage` by Country |
| 10 | High-infection countries' coverage | Scatter or table (infections vs ART) |
| 11 | ART vs mortality correlation | Scatter + trend line |
| 12 | Most at-risk countries | Table sorted by ART %, conditional format |

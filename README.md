# 🌍 Global HIV Trends & Treatment Effectiveness Analysis

A data-driven public-health analysis of the global HIV epidemic from **2000 to 2024**, built in **Power BI** on official **World Health Organization (WHO)** data covering **150 countries** across **6 WHO regions**.

> **Specialization:** Healthcare · **Business Focus:** Public Health Analysis · **Tool:** Power BI

🔗 **Live project site:** open `index.html` (host on GitHub Pages) · 🎛️ **Interactive dashboard:** `hiv_dashboard.html`

---

## 📌 Overview

Over three decades, the rollout of Antiretroviral Therapy (ART) has turned HIV from a fatal disease into a manageable chronic condition. This project evaluates whether that progress is real and sustainable by answering three core questions:

1. Are HIV infections truly declining?
2. Is increased treatment access reducing mortality?
3. How is the overall burden of HIV evolving globally?

---

## 🔑 Key Findings

| Indicator (2024) | Value | Trend |
|---|---|---|
| New infections | ~984K | ↓ 59% since 2000 |
| HIV deaths | ~523K | ↓ 70% from 2004 peak |
| People living with HIV | ~35.2M | ↑ rising prevalence |
| ART coverage (PLHIV-weighted) | ~79% | ↑ ~10× since 2000 |
| Death rate / survival rate | 1.49% / 98.51% | — |

- **Infections are falling but plateauing** — barely changed from 2023 to 2024 (+0.25%).
- **Treatment works** — ART coverage and death rate are negatively correlated (**r ≈ −0.48**, 108 countries).
- **Africa carries ~75% of the burden**, yet the highest *death rates* are in the Eastern Mediterranean (3.81%) and Western Pacific (2.71%).
- **At-risk countries** (high burden, low ART): Pakistan (16%), Sudan (20%), Madagascar (29%), Congo (34%), Indonesia (41%), Philippines (40%).

---

## ❓ The 12 Business Questions — Answered

| # | Question | Answer |
|---|---|---|
| 1 | New infections over time | Fell 59%: 2.40M (2000) → 0.98M (2024); plateauing |
| 2 | Total HIV deaths globally | ~523K in 2024; ↓70% from the 2004 peak |
| 3 | People living with HIV | ~35.2M latest year and rising |
| 4 | Overall treatment coverage | ~66% avg / ~79% PLHIV-weighted ART (2024) |
| 5 | Mortality & survival rate | Death rate ≈ 1.49%; survival ≈ 98.51% |
| 6 | Highest deaths per 1,000 infections | Lesotho, Ethiopia, Malawi, Zimbabwe, Thailand |
| 7 | Infections vs deaths trend | Both falling; deaths fell faster |
| 8 | Most affected regions | Africa (~75%); top death rates in E. Med. & W. Pacific |
| 9 | Best treatment access | Rwanda, Botswana, Namibia, Zimbabwe, Eswatini, Zambia (90%+) |
| 10 | High-infection countries' coverage | Mostly adequate; Indonesia (41%) & Philippines (40%) lag |
| 11 | ART coverage vs mortality | Negative correlation (r ≈ −0.48) |
| 12 | Most at-risk countries | Pakistan, Sudan, Madagascar, Congo, Indonesia |

---

## 🗂️ Data Model — Star Schema

```
                 ┌──────────────────┐
                 │   Dim_Date (25)   │
                 │  Year, Decade,    │
                 │  ART_Era          │
                 └─────────┬─────────┘
                           │
┌────────────────┐   ┌─────▼──────────┐   ┌──────────────────┐
│ Dim_Geography  │──▶│   Fact_HIV     │◀──│  Dim_Indicator   │
│ (150)          │   │  (9,763 rows)  │   │  (4)             │
│ Country,       │   │ IndicatorKey,  │   │ Indicator,       │
│ RegionCode,    │   │ CountryKey,    │   │ Measure_Type,    │
│ WHO_Region     │   │ DateKey, Value │   │ Unit             │
└────────────────┘   └────────────────┘   └──────────────────┘
```

**KPIs (DAX):** Death Rate = Deaths ÷ PLHIV · Survival Rate = 1 − Death Rate · Deaths per 1,000 Infections · ART Coverage (PLHIV-weighted) · YoY Growth.
Full DAX in [`docs/PowerBI_Step_by_Step_Guide.md`](docs/PowerBI_Step_by_Step_Guide.md).

---

## 💡 Insights & ✅ Recommendations

Full text in [`docs/Insights_and_Recommendations.md`](docs/Insights_and_Recommendations.md) and in the slide deck. In brief:

**Insights** — Rising prevalence reflects treatment success, not worse infections; Africa bears the heaviest burden; in many countries deaths fall faster than infections; stronger ART coverage tracks lower mortality; but high "deaths per 1,000 infections" where coverage looks fine signals weak delivery systems.

**Recommendations** — (1) Close coverage gaps in low-ART, high-burden countries; (2) re-energise prevention; (3) fund the growing care pool; (4) target retention hotspots; (5) modernise data reporting.

---

## 📁 Repository Structure

```
├── index.html                         # 🌐 project landing site (GitHub Pages home)
├── hiv_dashboard.html                 # 🎛️ interactive web dashboard (self-contained)
├── report/
│   ├── HIV_Project_Presentation.pdf   # 18-slide capstone deck (submission)
│   ├── HIV_Project_Presentation.pptx
│   └── HIV_Analysis_Report.pdf        # detailed written report (+ .docx)
├── dashboard/
│   └── HIV_Dashboard.pbix             # your Power BI file
├── data/
│   ├── HIV_dataset.xlsx               # raw WHO data
│   └── HIV_Star_Schema_Model.xlsx     # cleaned fact + dimension tables
├── docs/
│   ├── PowerBI_Step_by_Step_Guide.md
│   ├── GitHub_Upload_Guide.md
│   └── Insights_and_Recommendations.md
├── README.md
└── .gitignore
```

> The links in `index.html` expect this layout (`report/`, `data/`, `docs/`, and `hiv_dashboard.html` at the root). Keep the folders as named and the buttons will work once hosted.

---

## 🚀 Host the site (GitHub Pages)

1. Push this repo to GitHub (see [`docs/GitHub_Upload_Guide.md`](docs/GitHub_Upload_Guide.md)).
2. Repo → **Settings → Pages** → Source: `main` branch, `/ (root)`.
3. Your site goes live at `https://<username>.github.io/<repo-name>/`.

---

## 📚 Data Source
World Health Organization (WHO) — Global HIV indicators, 2000–2024.

## 🙏 Acknowledgements
Capstone project for **10Alytics** (Healthcare specialization).

---
*Built with Power BI · Web build self-contained (charts & dashboard embedded).*

# 🌍 Global HIV Trends & Treatment Effectiveness Analysis

A data-driven public-health analysis of the global HIV epidemic from **2000 to 2024**, built in **Power BI** on official **World Health Organization (WHO)** data covering **150 countries** across **6 WHO regions**.

> **Specialization:** Healthcare  •  **Business Focus:** Public Health Analysis  •  **Tool:** Power BI

---

## 📌 Project Overview

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

- **Infections are falling but plateauing** — barely changed from 2023 to 2024 (+0.25%).
- **Treatment works** — ART coverage and death rate are negatively correlated (**r ≈ −0.48** across 108 countries).
- **Africa carries ~75% of the burden**, but the highest *death rates* are in the Eastern Mediterranean (3.81%) and Western Pacific (2.71%).
- **At-risk countries** pairing high burden with low ART coverage: Pakistan (16%), Sudan (20%), Madagascar (29%), Indonesia (41%), Philippines (40%).

---

## 🗂️ Data Model — Star Schema

A clean **star schema**: one fact table + three dimension tables.

```
                 ┌──────────────────┐
                 │   Dim_Date        │
                 │  Year, Decade,    │
                 │  ART_Era          │
                 └─────────┬─────────┘
                           │
┌────────────────┐   ┌─────▼──────────┐   ┌──────────────────┐
│ Dim_Geography  │──▶│   Fact_HIV     │◀──│  Dim_Indicator   │
│ Country,       │   │ IndicatorKey,  │   │ Indicator,       │
│ RegionCode,    │   │ CountryKey,    │   │ Measure_Type,    │
│ WHO_Region     │   │ DateKey, Value │   │ Unit             │
└────────────────┘   └────────────────┘   └──────────────────┘
```

| Table | Rows | Role |
|---|---|---|
| `Fact_HIV` | 9,763 | Measures (Value) + foreign keys |
| `Dim_Geography` | 150 | Country & WHO region |
| `Dim_Indicator` | 4 | Metric, category, unit |
| `Dim_Date` | 25 | Year, decade, ART era |

---

## 📊 KPIs (DAX)

| KPI | Definition |
|---|---|
| Death Rate | `HIV Deaths ÷ People Living with HIV` |
| Survival Rate | `1 − Death Rate` |
| Deaths per 1,000 Infections | `(HIV Deaths ÷ New Infections) × 1000` |
| Avg ART Coverage | `AVERAGE(ART coverage %)` (PLHIV-weighted globally) |
| Infections YoY % | `(This year − Prior year) ÷ Prior year` |

Full DAX is in [`docs/PowerBI_Step_by_Step_Guide.md`](docs/PowerBI_Step_by_Step_Guide.md).

---

## 📁 Repository Structure

```
├── data/
│   ├── HIV_dataset.xlsx              # raw WHO data
│   └── HIV_Star_Schema_Model.xlsx    # cleaned fact + dimension tables
├── dashboard/
│   └── HIV_Dashboard.pbix            # Power BI report
├── report/
│   ├── HIV_Analysis_Report.pdf       # detailed written analysis
│   └── HIV_Presentation.pdf          # data-storytelling slide deck
├── docs/
│   ├── PowerBI_Step_by_Step_Guide.md # how to rebuild the dashboard
│   └── GitHub_Upload_Guide.md        # how this repo was published
└── README.md
```

---

## 🚀 How to Reproduce

1. Open `data/HIV_Star_Schema_Model.xlsx` (or `HIV_dataset.xlsx`) in **Power BI Desktop**.
2. Follow [`docs/PowerBI_Step_by_Step_Guide.md`](docs/PowerBI_Step_by_Step_Guide.md) to model, measure and visualise.
3. Open `dashboard/HIV_Dashboard.pbix` to explore the finished interactive report.

---

## ✅ Recommendations (summary)

1. **Close coverage gaps** in low-ART, high-burden countries (Pakistan, Indonesia, Madagascar, Congo, Sudan).
2. **Re-energise prevention** as new infections plateau.
3. **Fund the growing care pool** — sustainable, long-term ART financing.
4. **Target retention hotspots** (Lesotho, Ethiopia, Malawi, Zimbabwe).
5. **Modernise data reporting** — move five-yearly indicators to annual.

---

## 📚 Data Source
World Health Organization (WHO) — Global HIV indicators, 2000–2024.

## 🙏 Acknowledgements
Capstone project for **10Alytics** (Healthcare specialization).

---
*Built with Power BI • Analysis & documentation by the project author.*

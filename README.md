# Olist E-Commerce Performance Dashboard (Power BI)

## 📌 Project Overview
This project analyzes the **Olist Brazilian E-Commerce Dataset** to uncover insights into delivery performance, seller quality, and payment behavior across Brazil. Built entirely in Power BI, the dashboard connects multiple relational tables using Power Query and DAX to answer real business questions faced by e-commerce operations teams.

**Core question:** *Which operational and transactional factors drive customer satisfaction in Brazilian e-commerce — and where do they break down?*

## 🎯 Business Objectives & Research Questions
1. **Delivery Performance & Customer Satisfaction** *(primary focus)*
   - Is there a relationship between delivery delay and review score?
   - Which states suffer the most delays?

2. **Seller Performance Segmentation**
   - Which sellers combine high order volume with low customer satisfaction?
   - What share of sellers qualify as "high-risk" (high delay + low rating)?

3. **Payment Behavior Analysis**
   - How does payment type (credit card, boleto, voucher, debit card) relate to order value?
   - Is installment count linked to higher order value?

## 🗂️ Data Source
[Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — imported as 9 raw CSV files; the final data model uses 6 core tables (orders, order_items, customers, sellers, payments, reviews) plus a custom date table. The products table was excluded from the final model as it wasn't required for the analysis. Coverage: **September 2016 – August 2018** (the report filters to Jan 2017–Aug 2018 to exclude incomplete boundary months).

## 🛠️ Tech Stack & Process
- **Power Query (M)** — imported raw CSV files, cleaned data types, fixed a German-locale decimal separator bug on price fields, handled nulls, merged/appended queries
- **Data Modeling** — star schema in Power BI: fact tables (orders, order_items) linked to dimension tables (customers, sellers, payments, reviews) and a custom date table. Full model diagram: [`docs/data-model.png`](docs/data-model.png)
- **DAX** — custom measures and calculated columns (delivery delay in days, delay buckets, seller-level delay rate, high-risk seller flag via RANKX/FILTER, on-time delivery rate, average order value corrected for duplicate payment rows). Full list with descriptions: [`docs/dax-dictionary.md`](docs/dax-dictionary.md)
- **Power BI** — 4-page report with custom sidebar navigation, KPI cards, and cross-page filtering

## 📊 Dashboard Pages
| Page | Focus |
|---|---|
| 1. Executive Overview | Company-wide KPIs, summary cards linking to each analysis page, revenue trend, payment split, review distribution |
| 2. Delivery Performance & Customer Satisfaction | Delay buckets vs. review score, delivery speed trend, late delivery rate by state |
| 3. Seller Performance Segmentation | Risk quadrant (delay rate vs. review score), top 10 highest-risk sellers, revenue by seller state |
| 4. Payment Behavior Analysis | Payment type split, revenue and average order value by payment type, order value by installment count |

Screenshots: [`docs/screenshots/`](docs/screenshots/) · Full report export: [`report.pdf`](report.pdf)

## 🔑 Key Insights
- **Delivery delay is the strongest driver of dissatisfaction:** average review score drops from **4.21** (on-time/early) to **1.70** (8+ days late) — a ~60% decline. Orders delivered 8+ days late average **1.7/5**, while on-time deliveries average **4.2/5**.
- Only **6.64%** of orders are delayed overall, but the impact on satisfaction is disproportionate — the average delivery runs ~12 days, well within the ~12-day buffer built into estimated delivery dates.
- **Alagoas (AL)** has the highest late delivery rate at **20.78%**, nearly 3x the national average, followed by Maranhão (MA) and Sergipe (SE) — delay risk is concentrated in Brazil's North/Northeast states.
- **8.68%** of active sellers qualify as high-risk (delay rate >15% with a statistically reliable order count) — roughly 1 in 12 sellers needs operational attention.
- **75.3%** of orders are paid by credit card, with an average order value of **164 BRL** — notably higher than boleto (145) or debit card (143), suggesting credit card users make larger purchases.
- Average order value generally rises with installment count (120 BRL at 1 installment → 400+ BRL at 10+ installments), confirming customers use installments primarily for higher-value purchases.

## 💡 Recommendations
- **Prioritize the high-risk seller segment (8.68%)** for direct operational outreach — since these sellers combine high delay rates with statistically reliable order volume, resolving even a fraction of their delays would measurably lift the platform-wide review score.
- **Focus logistics investment on North/Northeast states** (AL, MA, SE, PI, CE) — these carry the highest late delivery rates and represent the clearest lever for improving nationwide satisfaction.
- **Set an internal SLA alert at the 4–7 day delay mark** rather than waiting for 8+ days — review scores already drop sharply at this stage (4.21 → 2.10), so earlier intervention could prevent orders from crossing into the most damaging delay bucket.
- **Leverage installment flexibility as a sales lever for higher-value items**, since average order value scales with installment count — this pattern could inform targeted promotions for higher-ticket products.

## 📁 Repository Structure
```
portfoili-Projekt/
├── README.md
├── report.pdf
├── docs/
│   ├── data-model.png          ← full data model / relationships diagram
│   ├── dax-dictionary.md       ← all measures & calculated columns explained
│   └── screenshots/            ← 4 dashboard page exports (PNG)
└── pbip/                       ← Power BI Project format (open the .pbip file with Power BI Desktop)
    ├── Olist_Ecommerce_Dashboard.pbip
    ├── Olist_Ecommerce_Dashboard.Report/
    ├── Olist_Ecommerce_Dashboard.SemanticModel/
    └── .gitignore
```
**To view the report:** download the entire `pbip/` folder (all files must stay together), then open `Olist_Ecommerce_Dashboard.pbip` in Power BI Desktop.

# Supply Chain & Inventory Performance Dashboard

An end-to-end Power BI project analyzing supply chain and delivery performance using the DataCo Smart Supply Chain dataset — from raw data cleaning through a star-schema data model, 15+ DAX measures, and a 3-page interactive dashboard.

**Author:** Neelmadhav Lingam
**Tools:** Power BI, Power Query (M), DAX
**Dataset:** [DataCo Smart Supply Chain](https://www.kaggle.com/datasets/shashwatwork/dataco-smart-supply-chain-for-big-data-analysis) (Kaggle) — 180,519 order line items across 66K distinct orders, Jan 2015–Jan 2018

---

## Project Overview

This project simulates a real-world operations analytics engagement: taking a large, messy transactional dataset and turning it into a decision-ready dashboard that surfaces delivery performance issues, profitability drivers, and customer/product insights.

## Data Cleaning (Power Query)

Key issues identified and resolved during cleaning:

- **Locale-based date parsing bug** — dates in `M/D/YYYY` format were being misread under the system's regional settings; fixed by explicitly parsing with the correct locale.
- **Broken date relationship** — datetime columns carried a time-of-day component that silently broke the relationship to `DimDate`; resolved by converting to pure `Date` type before modeling.
- **PII and unused columns removed** — email, password, customer name, street address, and product image columns were dropped, as they carried no analytical value and unnecessary personal data.
- **Data integrity checks** — verified no duplicate order line items and no nulls in key delivery fields (ship dates, delivery status).
- **Fixed mixed-language country labels** — `Customer Country` contained non-English entries (e.g., "EE. UU." for USA); standardized to consistent English naming across the column.

## Data Model

A star schema built for analytical performance and clarity:

- **Fact table:** `FactOrders`
- **Dimension tables:** `DimCustomer`, `DimProduct`, `DimDepartment`, `DimDate` (custom-built calendar table)
- **Dual date relationships:** an active relationship on Order Date and an inactive relationship on Shipping Date, toggled in DAX measures via `USERELATIONSHIP()` — allowing the same fact table to support both order-based and shipping-based time analysis without duplicating data.

## DAX Measures (15+)

A sample of the core measures built:

```dax
Late Delivery % =
DIVIDE(
    CALCULATE([Total Orders], FactOrders[Late_delivery_risk] = 1),
    [Total Orders]
)

Avg Days for Shipping (Real) =
AVERAGE(FactOrders[Days for shipping (real)])

Shipping Delay (Days) =
[Avg Days for Shipping (Real)] - [Avg Days for Shipping (Scheduled)]

Orders Shipped (by Ship Date) =
CALCULATE(
    [Total Orders],
    USERELATIONSHIP(FactOrders[Shipping Date], DimDate[Date])
)
```

Full measure list includes: Total Sales, Total Orders, Total Profit, Profit Margin %, Avg Order Value, Late Delivery %, On-Time Delivery %, Avg Days for Shipping (Real/Scheduled), Shipping Delay (Days), Cancellation/Fulfillment Rate %, Total Products, Total Customers, and Orders Shipped (by Ship Date).

## Dashboard Pages

### 1. Executive Overview
KPI cards, order trend (order date vs. ship date), sales by category and region.

![Executive Overview](images/executive-overview.png)

### 2. Delivery Performance
Late delivery split, delay breakdown by shipping mode and region.

![Delivery Performance](images/delivery-performance.png)

### 3. Product & Customer Insights
Top products, customer segment and geography breakdown.

![Product & Customer Insights](images/product-customer-insights.png)

## Key Insights

- **54.8% of orders were delivered late**, driven by an average shipping delay of 0.57 days (3.50 actual vs. 2.93 scheduled days).
- **First Class shipping had the highest late-delivery rate at 95.35%**, compared to 76.63% for Second Class — counterintuitively, the "premium" shipping option performed worst on reliability.
- **Fishing and Cleats led all categories by revenue**, totaling ₹36.78M in sales at a 10.78% profit margin.
- **Western Europe and Central America were the top regions by sales**, with the Consumer segment leading customer segments overall.

## What's Next

- Investigate root causes behind First Class shipping's high late-delivery rate
- Add drill-through pages for individual product and customer deep-dives
- Explore a predictive model for late-delivery risk using the existing feature set

---

## Repository Structure

```
supply-chain-dashboard/
│
├── README.md                          # Project overview (this file)
├── .gitignore                         # Excludes OS/Power BI temp files and raw data
│
├── docs/
│   └── data-model-diagram.md          # Star schema (Mermaid diagram) + notes on the
│                                       # dual date-relationship design
│
└── images/                            # Dashboard screenshots, one per report page
    ├── executive-overview.png         # Page 1 — KPI cards, order trend, sales by category/region
    ├── delivery-performance.png       # Page 2 — late delivery %, delay by shipping mode/region
    └── product-customer-insights.png  # Page 3 — top products, segment & geography breakdown
```

**Where to start reading:**
- New to the project? Start with **Project Overview** and **Key Insights** above for the headline findings.
- Want the technical details? **Data Cleaning** and **Data Model** cover the Power Query and star-schema work; `docs/data-model-diagram.md` has the full entity-relationship diagram.
- Want to see the DAX itself? See **DAX Measures** above for sample formulas and the full measure list.
- Looking for the actual `.pbix` Power BI file? *(Add it to the repo root and link it here once uploaded — GitHub doesn't preview `.pbix` files, but visitors can download and open them in Power BI Desktop.)*

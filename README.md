# Supply Chain Performance Dashboard (Power BI)

An executive Power BI dashboard that tracks **Lead Time, Fill Rate, Order Accuracy, On-Time Delivery and Inventory Turnover**, built on a star-schema data model with custom DAX measures, drill-through pages, bookmarks and dynamic filters.

![Executive Overview](screenshots/01_executive_overview.png)

---

## 1. Business Problem

The Supply Chain Head had no single view of delivery and inventory performance. Reports were static, mixed different KPI definitions and could not be broken down by region, product or shipping mode. The goal was a dashboard that answers: *Are we delivering on time? Where are delays concentrated? How efficiently is inventory turning?*

## 2. Stakeholder Requirements (BRD summary)

| Requirement | Solution |
|---|---|
| See delivery performance at a glance | Executive Overview with KPI cards |
| Find where delays happen | Delivery Performance page by region and shipping mode |
| Understand product profitability | Product and Region Analysis page |
| Investigate specific orders | Drill-through Order Detail page |
| Trust the numbers | Single KPI glossary and a testing checklist |

Full KPI definitions are in [`dax_measures.md`](dax_measures.md).

## 3. KPIs

On-Time Delivery %, Late Delivery %, Avg Lead Time and variance vs plan, Fill Rate %, Order Accuracy %, Perfect Order Rate %, Inventory Turnover, Days Inventory Outstanding, Sales, Profit, Margin, YoY and MoM growth.

## 4. Dataset

- **Source:** DataCo Smart Supply Chain dataset (Kaggle) - [add link]
- **Size:** ~[180,000] order lines, [53] columns, [2015 to 2018]
- **Inventory data:** the source has no stock levels, so a **simulated monthly inventory table** was generated with a documented script (see `dax_measures.md`, section 2). Inventory Turnover and Days Inventory Outstanding therefore illustrate the method and are **not real company figures**.
- **Definition notes:** Fill Rate and Order Accuracy are status-based proxies, because the dataset has no quantity-shipped field. COGS is approximated as Sales minus Profit.

## 5. Data Model

![Data model](screenshots/data_model.png)

Star schema with `Fact_Orders` and `Fact_Inventory` at the centre, and `Dim_Date`, `Dim_Product` and `Dim_Customer` around them. All relationships are one-to-many with single-direction filtering.

## 6. Approach

1. Gather requirements and define KPIs (BRD).
2. **Power Query:** fix encoding, rename columns, change data types, remove duplicates, create `COGS`, split fact and dimensions.
3. **Model:** build the star schema, create the `Dim_Date` table and mark it as the date table.
4. **DAX:** write measures in a dedicated `_Measures` table (core, delivery, fill rate, inventory, time intelligence).
5. **Report:** build four pages, with consistent colours and layout.
6. **Interactivity:** drill-through, bookmarks, synced slicers, tooltips and a field parameter.
7. **Test:** reconcile KPIs against Excel (see [`testing_checklist.md`](testing_checklist.md)).

## 7. Report Pages

| Page | What it answers |
|---|---|
| Executive Overview | How are we doing overall? |
| Delivery Performance | Where and why are deliveries late? |
| Product and Region Analysis | Which products and regions drive profit? |
| Order Detail (drill-through) | Which exact orders caused the problem? |

## 8. Key Insights

> Replace the placeholders with your actual numbers.

1. On-Time Delivery is **[X]%**, and **[Shipping Mode]** has the worst late rate at **[X]%**.
2. Average lead time is **[X] days** against **[X] days** scheduled (variance **[X]**).
3. **[Region]** has the highest sales but the lowest on-time rate (**[X]%**).
4. Fill Rate is **[X]%**, with **[X]%** of orders cancelled or flagged as fraud.
5. [Insight on the simulated inventory turnover, clearly labelled as illustrative].

## 9. Recommendations

- Renegotiate or replace the carrier / shipping mode with the highest late rate.
- Reset scheduled lead times in **[Region]** to realistic levels, or add buffer stock.
- Investigate the main cause of cancelled orders.

## 10. How to Open

1. Install **Power BI Desktop** (free).
2. Download `powerbi/Supply_Chain_Dashboard.pbix`.
3. If the data source path breaks, go to **Home > Transform data > Data source settings** and point to the `data/` folder.
4. A PDF export and screenshots are also included for quick viewing.

## 11. Repository Structure

```
supply-chain-powerbi-dashboard/
├── README.md
├── dax_measures.md
├── testing_checklist.md
├── insights.md
├── data/
│   ├── raw/                (dataset link or sample)
│   └── Fact_Inventory.csv  (simulated)
├── powerbi/
│   └── Supply_Chain_Dashboard.pbix
├── docs/
│   └── BRD.md
└── screenshots/
    ├── 01_executive_overview.png
    ├── 02_delivery_performance.png
    ├── 03_product_region.png
    ├── 04_drillthrough.png
    ├── data_model.png
    └── dashboard_demo.gif
```

## 12. Learnings and Next Steps

- Defining KPIs before building prevented rework and disagreements on numbers.
- Next: connect to real inventory data and add a forecasting page.
- Next: publish to Power BI Service with scheduled refresh.

---
**Author:** Yash Katara | [LinkedIn](https://linkedin.com/in/yash-katara-7185371b0) | yashkatara2016@gmail.com

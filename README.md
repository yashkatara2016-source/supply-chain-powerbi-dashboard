# Supply Chain Delivery Performance Dashboard (Power BI)

A single-page Power BI dashboard that tracks **On-Time (OT %) and In-Full (IF %) delivery** across regions, and analyses **delay days by warehouse and transport mode**, delivered vs ordered quantity, and inventory stock by product category.

![Dashboard Overview](screenshots/dashboard_overview.png)

---

## 1. Business Problem

The supply chain team needs to know how reliably orders are delivered on time and in full, and where delays come from. Without one consolidated view, it is hard to compare regions, warehouses and transport modes, or to decide where to act first.

Full requirements are in [`docs/BRD.md`](docs/BRD.md).

## 2. Objectives

- Measure On-Time % and In-Full % overall and by region
- Identify which warehouses and transport modes are linked to the most delay days
- Compare delivered quantity with ordered quantity
- Show inventory stock by product category
- Let users filter the whole page by product category

## 3. KPIs

| KPI | Definition |
|---|---|
| Total Orders | Number of orders in the dataset |
| OT % | Share of orders delivered on time (average of the 1/0 `OT` flag) |
| IF % | Share of orders delivered in full (average of the 1/0 `IF` flag) |
| Average Delay Days | Average of the `DelayDays` column |

## 4. Dataset

- **Source:** [add source, e.g. Kaggle link or "self-created practice dataset"]
- **Size:** 100 orders, [X] columns
- **Period:** [add date range]
- **Table name in the model:** `SCM Data`
- **Key fields:** Order Date, Product ID, Product Category, Region, Warehouse Location, Transport Mode, Ordered Qty, Delivered Qty, OT, IF, DelayDays, Inventory Stock

## 5. DAX Measures

| Measure | DAX |
|---|---|
| OT % | `AVERAGE('SCM Data'[OT])` |
| IF % | `AVERAGE('SCM Data'[IF])` |
| Average Delays Days | `AVERAGE('SCM Data'[DelayDays])` |

All measures, field mapping and validation steps are documented in [`dax_measures.md`](dax_measures.md).

## 6. Approach

1. Load the data into Power BI and clean it in Power Query (data types, nulls, duplicates).
2. Create the DAX measures for OT %, IF % and average delay days.
3. Build the single-page report with KPI cards, regional, warehouse and transport mode visuals, a quantity comparison, an inventory treemap and a Product Category slicer.
4. Validate the headline numbers against an independent calculation in Excel (see [`testing_checklist.md`](testing_checklist.md)).

## 7. Dashboard Contents

| Visual | What it shows |
|---|---|
| KPI cards | Total Orders, OT %, IF % |
| Product Category slicer | Filters every visual on the page |
| Product ID with Total Quantity and Region | Quantity by product, split by region (ribbon chart) |
| IF % by Region | In-full performance for East, North, West and South |
| Inventory stock by Product Category with Order Date | Stock across Electronics, Furniture and Office Supplies (treemap) |
| Average Delay Days by Warehouse Location | Delay comparison across Bangalore, Delhi, Mumbai, Pune and Chennai (funnel) |
| Average Delay Days by Transport Mode | Truck, Air and Ship (donut) |
| Delivered Qty and Ordered Qty by Month | Fulfilment gap over time |

## 8. Key Insights

> Numbers marked with ~ are read from chart labels. Check them by hovering over the visuals and correct if needed. Fill the `[ ]` items from your dashboard.

1. Out of **100 orders**, **67%** are delivered on time and **53%** in full, so roughly one in three orders is late and nearly half are not delivered in full.
2.  **South** has the lowest (~[32]%).
3. By transport mode, **Truck** has the largest share (**38.85%**), followed by **Ship** (**33.83%**) and **Air** (**27.32%**).
4. Average delay is [X] days overall, and it is similar across warehouses: **Chennai** is about **85.7%** of the level of the highest warehouse (Bangalore).
5. Delivered quantity is about **3.2K** against **3.3K** ordered, a gap of only about 3% of units. The In-Full gap at order level (47% of orders) therefore comes from many small shortfalls rather than a few large ones. [Confirm this against how your `IF` flag is defined.]

## 9. Recommendations

- Review fulfilment and stock planning in **South**, **West** and **North**, which are well below East on In-Full %.
- Investigate why **Truck** and **Ship** deliveries carry most of the delay and agree delivery targets with the carriers.
- Introduce a tolerance rule or buffer stock for small shortfalls so that near-complete orders do not count as not in full.

## 10. Limitations

- The dataset is small (100 orders), so findings are illustrative and not statistically strong.
- The Delivered vs Ordered chart covers [one month / the available period] only, so no trend can be concluded.
- [Add anything else, e.g. no cost data, single warehouse per order.]

## 11. How to Open

1. Install **Power BI Desktop** (free).
2. Download `powerbi/[your file name].pbix` from this repository.
3. Open it in Power BI Desktop.
4. If the data source path breaks, go to **Home > Transform data > Data source settings** and point it to the data file.

## 12. Repository Structure

```
supply-chain-powerbi-dashboard/
├── README.md
├── dax_measures.md
├── testing_checklist.md
├── insights.md
├── docs/
│   └── BRD.md
├── powerbi/
│   └── [your file name].pbix
└── screenshots/
    ├── dashboard_overview.png
    └── data_model.png
```

## 13. Learnings and Next Steps

- Defining the KPIs before building the visuals keeps the dashboard focused.
- Next: add an On-Time In-Full (OTIF) measure and a drill-through page with order-level detail.
- Next: replace the pie-style delay view with a bar chart of average delay days by transport mode.

---
**Author:** Yash Katara | [LinkedIn](https://linkedin.com/in/yash-katara-7185371b0) | yashkatara2016@gmail.com

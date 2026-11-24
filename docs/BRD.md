# Business Requirements Document (BRD)
## Supply Chain Delivery Performance Dashboard

| | |
|---|---|
| **Project** | Supply Chain Delivery Performance Dashboard |
| **Tool** | Microsoft Power BI |
| **Prepared by** | Yash Katara |
| **Version** | 1.0 |
| **Date** | November 2026 |
| **Status** | Draft |

> **Note:** This is a portfolio project built on a 100-order dataset. The business scenario and stakeholders are illustrative.

---

## 1. Purpose

This document defines the business needs, KPIs and requirements for a single-page Power BI dashboard that gives the Supply Chain team one view of on-time delivery, in-full delivery, delays and inventory.

## 2. Business Problem

Delivery performance is reviewed through separate reports, so it is hard to see:

- how many orders arrive on time and how many arrive in full,
- which regions, warehouses and transport modes are weakest,
- how much of the ordered quantity is actually delivered,
- how stock is spread across product categories.

Without one consolidated view, the team cannot decide quickly where to act first.

## 3. Business Objectives

| # | Objective | How success is measured |
|---|---|---|
| 1 | Show overall delivery reliability | On-Time % and In-Full % are visible at a glance |
| 2 | Find weak regions | In-Full % can be compared across regions |
| 3 | Find the sources of delay | Delay days can be compared by warehouse and by transport mode |
| 4 | Track fulfilment against orders | Delivered quantity is compared with ordered quantity by month |
| 5 | Show inventory position | Stock is visible by product category |
| 6 | Allow simple self-service filtering | Users can filter all visuals by product category |

## 4. Stakeholders

| Stakeholder | Role | Main interest |
|---|---|---|
| Supply Chain Manager | Primary user | Overall On-Time and In-Full performance |
| Logistics Manager | User | Delay by transport mode |
| Warehouse Managers | User | Delay by warehouse location |
| Regional Managers | User | In-Full performance in their region |
| Inventory Planner | User | Stock by product category |
| Business Analyst (author) | Builder | Requirements, KPI definitions, testing |

## 5. Scope

**In scope**
- On-Time % and In-Full % at overall and regional level
- Average delay days by warehouse location and by transport mode
- Delivered quantity versus ordered quantity by month
- Inventory stock by product category
- Total quantity by product and region
- Filtering by product category

**Out of scope**
- Real-time data refresh
- Cost, profit and revenue analysis
- Demand forecasting
- Supplier analysis
- Row-level security

## 6. Key Business Questions

1. What percentage of orders is delivered on time, and what percentage in full?
2. Which region has the lowest In-Full %?
3. Which warehouse locations have the highest delays?
4. Which transport mode contributes the largest share of delay days?
5. How far is delivered quantity from ordered quantity?
6. How is inventory stock distributed across product categories?

## 7. KPI Requirements

| KPI | Definition |
|---|---|
| Total Orders | Count of orders |
| OT % (On-Time %) | Orders delivered on or before the promised date / total orders |
| IF % (In-Full %) | Orders delivered with the full ordered quantity / total orders |
| Average Delay (days) | Average number of days a delivery is later than promised |
| Delivered Qty / Ordered Qty | Sum of delivered units / sum of ordered units |

## 8. Functional Requirements

| ID | Requirement | Visual | Priority |
|---|---|---|---|
| FR-01 | Show Total Orders, OT % and IF % as headline KPI cards | KPI cards | Must have |
| FR-02 | Show In-Full % by region | Column chart | Must have |
| FR-03 | Show average delay by warehouse location | Funnel chart | Must have |
| FR-04 | Show the share of delay by transport mode | Donut chart | Must have |
| FR-05 | Compare delivered and ordered quantity by month | Stacked column chart | Must have |
| FR-06 | Show inventory stock by product category | Treemap | Should have |
| FR-07 | Show total quantity by product ID and region | Ribbon chart | Should have |
| FR-08 | Provide a Product Category slicer that filters all visuals | Slicer | Must have |

## 9. Non-Functional Requirements

- **Usability:** the whole report fits on one page and is understandable without training.
- **Consistency:** one colour palette, clear titles and labels on every visual.
- **Performance:** the page responds to filters within a few seconds.
- **Accuracy:** headline KPIs must match an independent calculation (see `testing_checklist.md`).

## 10. Data Requirements

| Item | Detail |
|---|---|
| Source | [add source, e.g. Kaggle link or self-created practice dataset] |
| Size | 100 orders, [X] columns |
| Period | [add date range] |
| Key fields | [Order ID, Product ID, Product Category, Region, Warehouse Location, Transport Mode, Order Date, Promised Date, Delivery Date, Ordered Qty, Delivered Qty, Inventory Stock] |
| Refresh | Static dataset, manual refresh only |

## 11. Assumptions and Limitations

1. The dataset is small (100 orders), so results are illustrative and not statistically strong.
2. On-Time and In-Full are calculated at order level using the definitions in section 7.
3. The data covers a limited period, so no long-term trend can be concluded.
4. [Add any other limitation, e.g. missing cost data.]

## 12. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| KPI definitions are unclear | Users distrust the numbers | Write each definition down and reconcile with the source data |
| Small dataset | Misleading conclusions | State the limitation in the README |
| Cluttered page | Low usability | Keep one clear message per visual, with readable titles |

## 13. Acceptance Criteria

The dashboard is accepted when:

- [ ] All visuals listed in section 8 are built.
- [ ] OT % and IF % follow the definitions in section 7.
      Headline KPIs match an independent calculation (see `testing_checklist.md`).
      The Product Category slicer filters all visuals.
- [ ] Known limitations are documented in the README.

## 14. Deliverables

1. Power BI report file (`.pbix`)
2. Dashboard screenshot
3. `dax_measures.md` (measure documentation)
4. `testing_checklist.md` (validation results)
5. This BRD

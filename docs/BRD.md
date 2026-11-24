# Business Requirements Document (BRD)
## Supply Chain Performance Dashboard

| | |
|---|---|
| **Project** | Supply Chain Performance Dashboard |
| **Tool** | Microsoft Power BI |
| **Prepared by** | Yash Katara |
| **Version** | 1.0 |
| **Date** | November 2026 |
| **Status** | Approved for build |

---

## 1. Purpose

This document defines the business needs, KPIs and requirements for a Power BI dashboard that gives the Supply Chain team one trusted view of delivery and inventory performance.

## 2. Business Problem

Supply chain performance is currently reviewed through static reports prepared manually. These reports:

- use different definitions of the same KPI (for example, "on-time delivery"),
- cannot be filtered by region, product or shipping mode,
- take time to prepare, so problems are found late.

As a result, the team cannot quickly see where deliveries are delayed or how efficiently stock is moving.

## 3. Business Objectives

| # | Objective | How success is measured |
|---|---|---|
| 1 | Give leadership a single view of delivery performance | One dashboard replaces the manual reports |
| 2 | Identify where and why deliveries are late | Late deliveries can be broken down by region, product and shipping mode |
| 3 | Standardise KPI definitions | Every KPI has one written definition, used across all pages |
| 4 | Make inventory efficiency visible | Inventory turnover and days of inventory are reported |
| 5 | Allow self-service analysis | Users can filter and drill down without asking an analyst |

## 4. Stakeholders

| Stakeholder | Role | Main interest |
|---|---|---|
| Supply Chain Head | Primary user and approver | Overall delivery and inventory performance |
| Logistics Manager | User | Shipping mode and carrier performance |
| Regional Operations Managers | User | Performance of their own region |
| Finance Team | Reviewer | Profit, margin and cost of goods sold |
| Business Analyst (author) | Builder | Requirements, KPI definitions, testing |

## 5. Scope

**In scope**
- Order, shipping and delivery analysis
- Fill rate, order accuracy and perfect order rate
- Inventory turnover and days of inventory outstanding
- Sales and profit trends with year-over-year and month-over-month comparison
- Filtering by year, region, product category and shipping mode
- Drill-through from a summary visual to order-level detail

**Out of scope**
- Real-time or live data refresh
- Demand forecasting
- Supplier and procurement analysis
- Row-level security by user

## 6. Key Business Questions

1. What percentage of orders are delivered on time, and how is this trending?
2. Which regions and shipping modes have the highest late-delivery rate?
3. How much longer does shipping take compared with the scheduled time?
4. What share of orders is fulfilled, and how many are cancelled or flagged as fraud?
5. Which products and regions generate the most and the least profit?
6. How quickly is inventory turning over?

## 7. KPI Requirements

| KPI | Definition |
|---|---|
| Average Lead Time | Average actual shipping days per order |
| Lead Time Variance | Actual lead time minus scheduled lead time |
| On-Time Delivery % | Orders delivered on time or early / orders shipped (excluding cancelled shipping) |
| Late Delivery % | Late orders / orders shipped |
| Fill Rate % | Fulfilled orders / orders that reached a final status |
| Order Accuracy % | Orders without a cancellation or fraud flag / all orders |
| Perfect Order Rate % | Orders both fulfilled and on time / orders with a final status |
| Inventory Turnover | Cost of goods sold / average inventory value |
| Days Inventory Outstanding | Days in the period / inventory turnover |
| Sales, Profit, Profit Margin % | Standard financial measures |
| YoY % and MoM % | Change compared with the same period last year / previous month |

Detailed DAX formulas are documented in `dax_measures.md`.

## 8. Functional Requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-01 | The dashboard shall show headline KPIs on an Executive Overview page | Must have |
| FR-02 | The dashboard shall provide a Delivery Performance page with breakdowns by region and shipping mode | Must have |
| FR-03 | The dashboard shall provide a Product and Region Analysis page | Must have |
| FR-04 | Users shall be able to drill through from a region or product to an order-level detail page | Must have |
| FR-05 | Slicers for year, region and shipping mode shall be available and synchronised across pages | Must have |
| FR-06 | Users shall be able to switch between views using bookmarks | Should have |
| FR-07 | KPI colours shall change by performance threshold (for example, green at or above 90% on-time delivery) | Should have |
| FR-08 | Hovering over a visual shall show additional context in a tooltip | Could have |

## 9. Non-Functional Requirements

- **Performance:** each page should load and respond to filters within a few seconds.
- **Usability:** a new user should understand each page without training.
- **Consistency:** one colour palette, one layout grid and clear labels across pages.
- **Accuracy:** headline KPIs must reconcile with an independent calculation (see `testing_checklist.md`).

## 10. Data Requirements

| Item | Detail |
|---|---|
| Source | DataCo Smart Supply Chain dataset (public, Kaggle) |
| Content | Orders, products, customers, shipping modes, delivery status, sales and profit |
| Period | 2015 to 2018 |
| Refresh | Static dataset, manual refresh only |

## 11. Assumptions and Constraints

1. The dataset has no inventory data, so a **simulated monthly inventory table** will be created. Inventory metrics are illustrative and will be labelled as such.
2. The dataset has no quantity-shipped field, so **Fill Rate and Order Accuracy are status-based proxies**.
3. Cost of goods sold is approximated as Sales minus Profit.
4. Data is historical, so the dashboard shows past performance only.

## 12. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| KPI definitions are disputed | Users distrust the numbers | Document each definition and get sign-off before building |
| Simulated inventory misleads readers | Wrong conclusions | Label inventory metrics clearly in the dashboard and README |
| Data quality issues (duplicates, wrong types) | Incorrect KPIs | Clean in Power Query and reconcile totals with the source |
| Dashboard becomes too cluttered | Low adoption | Limit each page to a few key visuals and test with a sample user |

## 13. Acceptance Criteria

The dashboard is accepted when:

- [ ] all four pages are built and match the requirements above,
- [ ] every KPI uses the agreed definition,
- [ ] headline KPIs reconcile with the source data (see `testing_checklist.md`),
- [ ] slicers, drill-through and bookmarks work as described,
- [ ] known limitations are documented in the README.

## 14. Deliverables

1. Power BI report file (`.pbix`)
2. Dashboard screenshots and PDF export
3. `dax_measures.md` (measure documentation)
4. `testing_checklist.md` (validation results)
5. This BRD

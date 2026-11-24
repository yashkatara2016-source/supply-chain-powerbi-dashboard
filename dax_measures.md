# DAX Measures and Data Model: Supply Chain Performance Dashboard

This file documents the data model, KPI definitions and every DAX measure used in the report. Copy each measure into Power BI Desktop (**Modeling > New measure**) and keep all of them inside one empty table called `_Measures`.

> **Before you start:** DataCo column names contain spaces and brackets. Rename them in Power Query as shown in section 1 so the DAX below works as written. If your column names differ, adjust only the names.

---

## 1. Source Columns and Renaming (Power Query)

Dataset: *DataCo Smart Supply Chain for Big Data Analysis* (Kaggle). Load the CSV with encoding **ISO-8859-1 / Latin-1**.

| Original column | Rename to | Used for |
|---|---|---|
| Order Id | `OrderID` | order count |
| order date (DateOrders) | `OrderDate` | date relationship |
| shipping date (DateOrders) | `ShipDate` | shipping analysis |
| Days for shipping (real) | `DaysShippedActual` | actual lead time |
| Days for shipment (scheduled) | `DaysShippedScheduled` | planned lead time |
| Delivery Status | `DeliveryStatus` | on-time analysis |
| Order Status | `OrderStatus` | fill rate, accuracy |
| Sales | `Sales` | revenue |
| Order Profit Per Order | `Profit` | profit |
| Order Item Quantity | `Quantity` | units |
| Order Item Discount | `Discount` | discount value |
| Product Card Id | `ProductKey` | product link |
| Customer Id | `CustomerKey` | customer link |
| Order Region | `Region` | geography filter |
| Shipping Mode | `ShippingMode` | shipping filter |

**Build the dimensions by referencing the raw query and keeping distinct rows:**
- `Dim_Product`: `ProductKey`, `ProductName` (from Product Name), `Category` (from Category Name), `Product Price`
- `Dim_Customer`: `CustomerKey`, `Segment` (from Customer Segment), `Country` (from Customer Country)

Then remove those descriptive columns from `Fact_Orders` so it keeps only keys and measures.

**Add one custom column in Power Query:**

```
COGS = [Sales] - [Profit]
```

COGS is approximated as Sales minus Profit because the dataset has no separate cost column. State this assumption in the README.

---

## 2. Data Model (Star Schema)

| Table | Type | Key | Relationship |
|---|---|---|---|
| `Fact_Orders` | Fact | OrderID (line level) | many-to-one to the dimensions below |
| `Fact_Inventory` | Fact (simulated) | ProductKey + MonthEnd | many-to-one to `Dim_Product` and `Dim_Date` |
| `Dim_Date` | Dimension | Date | `Fact_Orders[OrderDate]`, `Fact_Inventory[MonthEnd]` |
| `Dim_Product` | Dimension | ProductKey | `Fact_Orders[ProductKey]`, `Fact_Inventory[ProductKey]` |
| `Dim_Customer` | Dimension | CustomerKey | `Fact_Orders[CustomerKey]` |

Rules: single-direction filters from dimension to fact, no many-to-many, hide key columns in report view, mark `Dim_Date` as a date table.

### Date table (Modeling > New table)

```DAX
Dim_Date =
ADDCOLUMNS (
    CALENDAR (
        DATE ( YEAR ( MIN ( Fact_Orders[OrderDate] ) ), 1, 1 ),
        DATE ( YEAR ( MAX ( Fact_Orders[OrderDate] ) ), 12, 31 )
    ),
    "Year", YEAR ( [Date] ),
    "MonthNo", MONTH ( [Date] ),
    "Month", FORMAT ( [Date], "MMM" ),
    "YearMonth", FORMAT ( [Date], "YYYY-MM" ),
    "Quarter", "Q" & ROUNDUP ( MONTH ( [Date] ) / 3, 0 ),
    "MonthEnd", EOMONTH ( [Date], 0 )
)
```

Sort `Month` by `MonthNo`.

### Inventory table (simulated, disclose this in the README)

DataCo has no stock data, so create a monthly inventory snapshot. Everything in it is an **assumption**, and the README must say so.

```python
import pandas as pd, numpy as np

np.random.seed(42)
df = pd.read_csv("DataCoSupplyChainDataset.csv", encoding="latin-1")
df["OrderDate"] = pd.to_datetime(df["order date (DateOrders)"])
df["MonthEnd"] = df["OrderDate"].dt.to_period("M").dt.end_time.dt.normalize()

units = (df.groupby(["Product Card Id", "MonthEnd"])["Order Item Quantity"]
           .sum().reset_index()
           .rename(columns={"Product Card Id": "ProductKey",
                            "Order Item Quantity": "UnitsSold"}))

price = (df.groupby("Product Card Id")["Product Price"].mean()
           .reset_index().rename(columns={"Product Card Id": "ProductKey",
                                          "Product Price": "AvgPrice"}))
inv = units.merge(price, on="ProductKey")

# Assumptions: stock on hand = 1.2x to 2.5x of the month's sales; unit cost = 65% of price
inv["UnitsOnHand"]    = (inv["UnitsSold"] * np.random.uniform(1.2, 2.5, len(inv))).round().astype(int)
inv["UnitCost"]       = (inv["AvgPrice"] * 0.65).round(2)
inv["InventoryValue"] = inv["UnitsOnHand"] * inv["UnitCost"]

inv[["ProductKey", "MonthEnd", "UnitsOnHand", "UnitCost", "InventoryValue"]] \
    .to_csv("Fact_Inventory.csv", index=False)
```

---

## 3. KPI Definitions

| KPI | Definition | Why it matters |
|---|---|---|
| Lead Time | Average actual days from order to shipment | Speed of fulfilment |
| On-Time Delivery % | Orders delivered on time or early / orders shipped (excl. cancelled shipping) | Service reliability |
| Fill Rate % | Orders fulfilled (COMPLETE or CLOSED) / orders that reached a final status (fulfilled, cancelled, fraud) | Ability to complete demand |
| Order Accuracy % | Orders without a cancellation or fraud flag / all orders | Order-quality proxy |
| Perfect Order Rate % | Orders that are fulfilled **and** delivered on time / orders with a final status | Combined service quality |
| Inventory Turnover | COGS / average inventory value | How fast stock converts to sales |
| Days Inventory Outstanding | Days in period / inventory turnover | How long stock sits |

> **Note on proxies:** the dataset has no "quantity shipped vs quantity ordered" field, so Fill Rate and Order Accuracy are **status-based proxies**. Write this in the README. It is a normal and accepted approach when the data lacks the exact field.

---

## 4. Core Measures

```DAX
Total Orders =
DISTINCTCOUNT ( Fact_Orders[OrderID] )

Total Sales =
SUM ( Fact_Orders[Sales] )

Total Profit =
SUM ( Fact_Orders[Profit] )

Total COGS =
SUM ( Fact_Orders[COGS] )

Total Units =
SUM ( Fact_Orders[Quantity] )

Profit Margin % =
DIVIDE ( [Total Profit], [Total Sales] )

Avg Order Value =
DIVIDE ( [Total Sales], [Total Orders] )
```

---

## 5. Lead Time and Delivery Measures

```DAX
Avg Lead Time (Days) =
AVERAGE ( Fact_Orders[DaysShippedActual] )

Avg Scheduled Lead Time (Days) =
AVERAGE ( Fact_Orders[DaysShippedScheduled] )

Lead Time Variance (Days) =
[Avg Lead Time (Days)] - [Avg Scheduled Lead Time (Days)]

Shipped Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[DeliveryStatus] <> "Shipping canceled"
)

On-Time Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[DeliveryStatus] IN { "Advance shipping", "Shipping on time" }
)

Late Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[DeliveryStatus] = "Late delivery"
)

On-Time Delivery % =
DIVIDE ( [On-Time Orders], [Shipped Orders] )

Late Delivery % =
DIVIDE ( [Late Orders], [Shipped Orders] )
```

---

## 6. Fill Rate, Order Accuracy and Perfect Order

```DAX
Fulfilled Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[OrderStatus] IN { "COMPLETE", "CLOSED" }
)

Resolved Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[OrderStatus] IN { "COMPLETE", "CLOSED", "CANCELED", "SUSPECTED_FRAUD" }
)

Error Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[OrderStatus] IN { "CANCELED", "SUSPECTED_FRAUD" }
)

Fill Rate % =
DIVIDE ( [Fulfilled Orders], [Resolved Orders] )

Order Accuracy % =
DIVIDE ( [Total Orders] - [Error Orders], [Total Orders] )

Perfect Orders =
CALCULATE (
    [Total Orders],
    Fact_Orders[OrderStatus] IN { "COMPLETE", "CLOSED" },
    Fact_Orders[DeliveryStatus] IN { "Advance shipping", "Shipping on time" }
)

Perfect Order Rate % =
DIVIDE ( [Perfect Orders], [Resolved Orders] )
```

---

## 7. Inventory Measures

```DAX
Avg Inventory Value =
AVERAGEX (
    VALUES ( Dim_Date[YearMonth] ),
    CALCULATE ( SUM ( Fact_Inventory[InventoryValue] ) )
)

Inventory Turnover =
DIVIDE ( [Total COGS], [Avg Inventory Value] )

Inventory Turnover (Annualised) =
VAR MonthsInView = DISTINCTCOUNT ( Dim_Date[YearMonth] )
RETURN
    DIVIDE ( [Inventory Turnover] * 12, MonthsInView )

Days Inventory Outstanding =
VAR DaysInView = DATEDIFF ( MIN ( Dim_Date[Date] ), MAX ( Dim_Date[Date] ), DAY ) + 1
RETURN
    DIVIDE ( DaysInView, [Inventory Turnover] )
```

> `Dim_Date` must be filtered to the period you are viewing (use a Year slicer), otherwise these measures cover the full date range.

---

## 8. Time Intelligence

```DAX
Sales LY =
CALCULATE ( [Total Sales], SAMEPERIODLASTYEAR ( Dim_Date[Date] ) )

Sales YoY % =
DIVIDE ( [Total Sales] - [Sales LY], [Sales LY] )

Sales YTD =
TOTALYTD ( [Total Sales], Dim_Date[Date] )

Sales MTD =
TOTALMTD ( [Total Sales], Dim_Date[Date] )

Sales Previous Month =
CALCULATE ( [Total Sales], DATEADD ( Dim_Date[Date], -1, MONTH ) )

Sales MoM % =
DIVIDE ( [Total Sales] - [Sales Previous Month], [Sales Previous Month] )

Profit LY =
CALCULATE ( [Total Profit], SAMEPERIODLASTYEAR ( Dim_Date[Date] ) )

Profit YoY % =
DIVIDE ( [Total Profit] - [Profit LY], [Profit LY] )

Running Total Sales =
CALCULATE (
    [Total Sales],
    FILTER (
        ALLSELECTED ( Dim_Date[Date] ),
        Dim_Date[Date] <= MAX ( Dim_Date[Date] )
    )
)
```

---

## 9. Ranking, Dynamic Titles and Conditional Formatting

```DAX
Product Profit Rank =
RANKX ( ALLSELECTED ( Dim_Product[ProductName] ), [Total Profit], , DESC, DENSE )

Region Title =
"Delivery Performance: "
    & IF (
        ISFILTERED ( Fact_Orders[Region] ),
        SELECTEDVALUE ( Fact_Orders[Region], "Multiple regions" ),
        "All regions"
    )

OTD Colour =
SWITCH (
    TRUE (),
    [On-Time Delivery %] >= 0.90, "#2E7D32",
    [On-Time Delivery %] >= 0.80, "#F9A825",
    "#C62828"
)

Lead Time Status =
IF ( [Lead Time Variance (Days)] > 0, "Slower than plan", "On or ahead of plan" )
```

Use `OTD Colour` in **Conditional formatting > Format by > Field value**.

---

## 10. Report Pages and Features (what to build)

| Page | Visuals | Features |
|---|---|---|
| **Executive Overview** | KPI cards (Sales, Profit, Margin, OTD %, Fill Rate, Inventory Turnover), monthly trend, sales by region | Year / Region / Shipping Mode slicers |
| **Delivery Performance** | Lead time by shipping mode, late delivery % by region, delivery status split, variance by month | Bookmarks to switch between "Actual vs Scheduled" and "Late %" views |
| **Product and Region Analysis** | Top 10 products by profit, region map, matrix of category x region | Tooltips page, Top N filter |
| **Order Detail** (drill-through) | Table of orders with status, lead time, profit | **Drill-through** from any region or product |

**Interactivity checklist**
- [ ] Drill-through page from Region and Product visuals
- [ ] Bookmarks with buttons to toggle views
- [ ] Synced slicers across pages
- [ ] Field parameter to switch the metric (Modeling > New parameter > Fields)
- [ ] Custom tooltip page
- [ ] Dynamic title using `Region Title`

---

## 11. Validation and Testing (supports the "validated against business logic" claim)

Cross-check every headline KPI against an independent source, such as Excel or SQL, and record the result in `testing_checklist.md`.

| Test | Method | Expected |
|---|---|---|
| Total Orders | Excel: remove duplicates on Order Id, count | Matches `[Total Orders]` |
| Total Sales / Profit | Excel `SUM` on the raw column | Exact match |
| On-Time Delivery % | Excel `COUNTIFS` on Delivery Status | Matches within rounding |
| Lead time average | Excel `AVERAGE` of actual days | Exact match |
| Slicer behaviour | Select one region and one year | All visuals filter, KPI totals match a filtered pivot |
| Blank / divide-by-zero | Select a region with no data | Cards show blank, no errors |
| Relationship check | Model view | Only one-to-many, single direction |

Example row for `testing_checklist.md`:

```
| Test | Expected (Excel) | Actual (Power BI) | Pass |
| Total Sales | [value] | [value] | Yes |
```

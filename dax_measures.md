# DAX Measures and Field Mapping: Supply Chain Delivery Performance Dashboard

This file documents the measures and fields used in the report. It lists only what exists in the `.pbix` file.

---

## 1. Data Model

| Table | Description |
|---|---|
| `SCM Data` | Single flat table with one row per order |

**Columns used**

| Column | Meaning | Used for |
|---|---|---|
| Order ID / Order Date | Order identifier and date | Total Orders, month grouping |
| Product ID | Product code (P101 to P108) | Ribbon chart |
| Product Category | Electronics, Furniture, Office Supplies | Slicer, treemap |
| Region | East, North, West, South | IF % by Region, ribbon chart |
| Warehouse Location | Bangalore, Delhi, Mumbai, Pune, Chennai | Delay by warehouse |
| TransportMode | Truck, Air, Ship | Delay by transport mode |
| Ordered Qty / Delivered Qty | Units ordered and units delivered | Quantity comparison |
| OT | 1 if the order was delivered on time, else 0 | OT % |
| IF | 1 if the order was delivered in full, else 0 | IF % |
| DelayDays | Number of days of delay | Average Delay Days |
| Inventory Stock | Stock quantity | Treemap |

> Write down how `OT`, `IF` and `DelayDays` are calculated in your source data (for example, `OT = 1` when delivery date is on or before the promised date). Add it here: **[add definition]**

---

## 2. Measures

```DAX
OT % =
AVERAGE ( 'SCM Data'[OT] )

IF % =
AVERAGE ( 'SCM Data'[IF] )

Average Delays Days =
AVERAGE ( 'SCM Data'[DelayDays] )
```

Total Orders card (add the measure or field you used):

```DAX
Total Orders =
[paste your measure here, e.g. COUNTROWS ( 'SCM Data' ) or DISTINCTCOUNT ( 'SCM Data'[Order ID] )]
```

Because `OT` and `IF` are 1/0 flags, their average equals the percentage of orders that meet the condition, in whatever filter context the visual has (all orders on a KPI card, one region on the IF % by Region chart).

---

## 3. Visual to Field Mapping

| Visual | Fields | Measure |
|---|---|---|
| KPI cards | none (card values) | Total Orders, OT %, IF % |
| Product Category slicer | Product Category | none |
| Product ID with Total Quantity and Region (ribbon) | Axis: Product ID, Legend: Region, Values: [quantity field] | sum of quantity |
| IF % by Region (column) | Axis: Region | IF % |
| Inventory stock by Product Category (treemap) | Group: Product Category, Details: Order Date | sum of Inventory Stock |
| Average Delay Days by Warehouse Location (funnel) | Group: Warehouse Location | Average Delays Days |
| Average Delay Days by TransportMode (donut) | Legend: TransportMode | Average Delays Days |
| Delivered Qty and Ordered Qty by Month (stacked column) | Axis: Month_year | sum of Delivered Qty, sum of Ordered Qty |

Edit the rows above if any field name differs in your file.

---

## 4. Validation

Cross-check each headline number in Excel before relying on it:

| Measure | Excel check |
|---|---|
| Total Orders | `=COUNTA(OrderID_range)` |
| OT % | `=AVERAGE(OT_range)` |
| IF % | `=AVERAGE(IF_range)` |
| Average Delay Days | `=AVERAGE(DelayDays_range)` |
| IF % for one region | `=AVERAGEIFS(IF_range, Region_range, "East")` |

Results are recorded in [`testing_checklist.md`](testing_checklist.md).

---

## 5. Possible Extensions (not in the current report)

These are ideas for a later version and are **not** part of the current dashboard:

```DAX
OTIF % =
AVERAGEX (
    'SCM Data',
    IF ( 'SCM Data'[OT] = 1 && 'SCM Data'[IF] = 1, 1, 0 )
)

Quantity Fill % =
DIVIDE ( SUM ( 'SCM Data'[Delivered Qty] ), SUM ( 'SCM Data'[Ordered Qty] ) )
```

`OTIF %` can never be higher than `OT %` or `IF %`, which makes it a useful sanity check.

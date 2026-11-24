# Testing Checklist: Supply Chain Delivery Performance Dashboard

Fill the **Expected** column from Excel using the raw data. The **Actual** column shows what Power BI displays. A test passes when both match (allow rounding).

| # | Test | Excel check | Expected (Excel) | Actual (Power BI) | Pass |
|---|---|---|---|---|---|
| 1 | Total Orders | `=COUNTA(OrderID_range)` | | 100 | |
| 2 | OT % | `=AVERAGE(OT_range)` | | 67% | |
| 3 | IF % | `=AVERAGE(IF_range)` | | 53% | |
| 4 | Average Delay Days | `=AVERAGE(DelayDays_range)` | | [value] | |
| 5 | Total Delivered Qty | `=SUM(DeliveredQty_range)` | | ~3.2K | |
| 6 | Total Ordered Qty | `=SUM(OrderedQty_range)` | | ~3.3K | |
| 7 | IF % for East | `=AVERAGEIFS(IF_range, Region_range, "East")` | | [value] | |
| 8 | IF % for South | `=AVERAGEIFS(IF_range, Region_range, "South")` | | [value] | |
| 9 | Average delay for Truck | `=AVERAGEIFS(DelayDays_range, Mode_range, "Truck")` | | [value] | |
| 10 | Average delay for Bangalore | `=AVERAGEIFS(DelayDays_range, Warehouse_range, "Bangalore")` | | [value] | |

## Interaction tests

| # | Test | Expected | Pass |
|---|---|---|---|
| 11 | Select one value in the Product Category slicer | All visuals update, KPI cards change | |
| 12 | Clear the slicer | All visuals return to the full-data numbers | |
| 13 | Click a region bar | Other visuals cross-filter to that region | |
| 14 | Sum of the 3 transport mode shares | Equals 100% | |

## Issues found and fixed

| Issue | Cause | Fix |
|---|---|---|
| [describe any mismatch you found] | [cause] | [what you changed] |

> Tip: finding and fixing one mismatch is excellent interview material. Write it down, even if small.

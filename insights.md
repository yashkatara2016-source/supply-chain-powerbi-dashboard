# Insights: Supply Chain Delivery Performance Dashboard

> Numbers marked with ~ are read from chart labels. Check them in Power BI and correct if needed.

## 1. Overall delivery performance
- Total orders: **100**
- On-Time %: **67%** (about 1 in 3 orders is late)
- In-Full %: **53%** (nearly half the orders are not delivered in full)

## 2. Regional view
- Highest In-Full %: **East (~100%)**
- Lowest In-Full %: **South (~[32]%)**; **North** and **West** are also well below East
- Observation: the gap between East and the other regions is large, which points to a regional process difference

## 3. Delay analysis
- Delay by transport mode (share): **Truck 38.85%**, **Ship 33.83%**, **Air 27.32%**
- Delay by warehouse: Bangalore and Delhi are highest, Chennai is about **85.7%** of the highest level
- Average delay overall: **[X] days**
- Observation: delay is spread across all warehouses rather than concentrated in one

## 4. Quantity
- Delivered quantity: ~**3.2K** against ~**3.3K** ordered (about 3% gap)
- Observation: shortfalls per order are small, yet only 53% of orders count as in full. [Confirm with the definition of your `IF` flag.]

## 5. Inventory
- Stock is spread across Electronics, Furniture and Office Supplies. Largest stock category: **[category]**

## Recommendations
1. Investigate why **East** achieves near-complete in-full delivery and apply its practices to **South**, **West** and **North**.
2. Agree delivery targets with carriers for **Truck** and **Ship**, which carry most of the delay.
3. Set a tolerance rule or buffer stock for small shortfalls.
4. Monitor delay days by warehouse monthly to catch worsening trends early.

## Limitations
- Only 100 orders, so results are illustrative.
- The month-wise chart covers [one month / the available period] only.

# DAX Dictionary — Olist E-Commerce Performance Dashboard

This document lists all measures used in the report, exactly as built in Power BI (`__Measures` table). Naming prefixes (`__1_`, `_10_`, etc.) reflect build order and keep the Fields list organized.

## Core Measures

| # | Name | DAX | Description |
|---|---|---|---|
| 1 | `__1_Total_sales` | `SUM(olist_order_items_dataset[price])` | Sum of item price only (excludes freight). |
| 2 | `__2_Total_Freight` | `SUM(olist_order_items_dataset[freight_value])` | Sum of shipping/freight cost across all order items. |
| 3 | `__3_Total_Revenue` | `[__1_Total_sales] + [__2_Total_Freight]` | Total revenue combining item price and freight. |
| 4 | `__4_Total_Orders` | `DISTINCTCOUNT(olist_order_items_dataset[order_id])` | Total number of unique orders. |
| 5 | `__5_Average_Order_value` | `DIVIDE([__1_Total_sales], [__4_Total_Orders], 0)` | Average sales value per order. |
| 6 | `__6_Late_Deliveries` | `CALCULATE([__4_Total_Orders], olist_orders_dataset[order_delivered_customer_date] > olist_orders_dataset[order_estimated_delivery_date], NOT(ISBLANK(olist_orders_dataset[order_delivered_customer_date])))` | Count of orders delivered after the estimated delivery date (excludes orders not yet delivered). |
| 7 | `__7_Late_Delivery_Rate` | `DIVIDE([__6_Late_Deliveries], [__4_Total_Orders], 0)` | Share of orders delivered late. |
| 8 | `__8_Total_Payment` | `SUM(olist_order_payments_dataset[payment_value])` | Sum of full payment value, including installment fees — used on the Payment Behavior page. Differs from `__3_Total_Revenue` (item price + freight only), used on Overview. |
| 9 | `__9_Average_Review_Score` | `CALCULATE(AVERAGE(olist_order_reviews_dataset[review_score]), CROSSFILTER(olist_orders_dataset[order_id], olist_order_items_dataset[order_id], Both))` | Average customer review score (1–5), with bidirectional cross-filtering to allow filtering by seller/item context. |
| 10 | `_10_Average_Installments` | `AVERAGE(olist_order_payments_dataset[payment_installments])` | Average number of payment installments per order. |
| 11 | `_11_Average_payment_value` | `DIVIDE(SUM(olist_order_payments_dataset[payment_value]), DISTINCTCOUNT(olist_order_payments_dataset[order_id]))` | Average payment value per unique order (avoids double-counting split payments). |
| 12 | `_12_Avg_Delivery_delay` | `CALCULATE(AVERAGE(olist_orders_dataset[Delivery_Delay_Days]), NOT(ISBLANK(olist_orders_dataset[order_delivered_customer_date])), CROSSFILTER(olist_order_items_dataset[order_id], olist_orders_dataset[order_id], BOTH))` | Average delay in days vs. estimated delivery date (negative = delivered early on average). |
| 13 | `_13_Avg_Delivery_Days` | `CALCULATE(AVERAGEX(olist_orders_dataset, DATEDIFF(olist_orders_dataset[order_purchase_timestamp], olist_orders_dataset[order_delivered_customer_date], DAY)), NOT(ISBLANK(olist_orders_dataset[order_delivered_customer_date])), CROSSFILTER(olist_order_items_dataset[order_id], olist_orders_dataset[order_id], BOTH))` | Average actual delivery time in days, measured from purchase to customer delivery. |
| 14 | `_14_Seller_Delayed_Rate` | `DIVIDE(CALCULATE(COUNTROWS(olist_orders_dataset), olist_orders_dataset[Is_Delayed] = "Delayed"), CALCULATE(COUNTROWS(olist_orders_dataset)))` | Share of delayed orders, evaluated in seller context on the Seller Performance page. |
| 15 | `_15_Seller_Rank_Qualified` | `RANKX(FILTER(ALL(olist_sellers_dataset[seller_id]), CALCULATE(__Measures[__4_Total_Orders]) >= 10), [_14_Seller_Delayed_Rate], , DESC)` | Ranks sellers by delay rate, restricted to sellers with a statistically reliable order count (≥10). Used for the "Top Highest-Risk Sellers" chart. |
| 16 | `_16_Seller_Risk_Score` | `__Measures[_14_Seller_Delayed_Rate] * (5 - __Measures[__9_Average_Review_Score])` | Composite risk score combining delay rate and (inverted) review score — higher score means higher combined risk. |
| 17 | `_17_%_On_Time_Delivery` | `1 - [__7_Late_Delivery_Rate]` | Inverse of the late delivery rate, shown as a positive-framing KPI. |
| 18 | `_18_Active_Customers` | `DISTINCTCOUNT(olist_customers_dataset[customer_unique_id])` | Count of unique customers using `customer_unique_id` — not `customer_id`, which is generated per order in this dataset and would overstate the customer count. |
| — | `% High Risk Sellers` | `VAR QualifiedSellers = FILTER(ALL(olist_sellers_dataset[seller_id]), CALCULATE([__4_Total_Orders]) >= 10) VAR HighRiskCount = COUNTROWS(FILTER(QualifiedSellers, CALCULATE([_14_Seller_Delayed_Rate]) > 0.15)) VAR TotalQualified = COUNTROWS(QualifiedSellers) RETURN DIVIDE(HighRiskCount, TotalQualified)` | Share of qualified sellers (≥10 orders) with a delay rate above 15%. |

## Calculated Columns — `olist_orders_dataset`

| Name | Description |
|---|---|
| `Delivery_Delay_Days` | Days between estimated and actual delivery date. Negative = delivered early. |
| `Is_Delayed` | Text flag ("Delayed" / "On Time") derived from `Delivery_Delay_Days`, used by `_14_Seller_Delayed_Rate`. |
| `Delay_Bucket` | Groups orders into delay severity tiers (On Time/Early, 1-3, 4-7, 8+ Days Late) for the Delivery Performance page. |

## Calculated Columns — `olist_order_payments_dataset`

| Name | Description |
|---|---|
| `Installment_Group` | Groups `payment_installments` into 1, 2, 3 ... 10, 11, 12, +13 for chart display. |
| `Installment_Group_sort` | Numeric helper column to sort `Installment_Group` correctly (since it mixes numbers with the "+13" text label). |



# Week 1 Findings: Data Profiling & Structure Analysis

**Project:** Heavy Suppliers Warehouse Analytics (Individual Track)
**Scope:** 12 CSV tables, profiled for size, types, missing values, duplicates, relationships and business-rule consistency.

## 1. What the dataset contains

| Table | Rows | Columns | Notes |
|---|---|---|---|
| branches | 6 | 13 | Warehouses: Delhi, Pune, Chennai, Hyderabad, Kolkata, Ahmedabad |
| suppliers | 8 | 12 | Suppliers located in China |
| products | 30 | 23 | Heavy-machinery parts |
| customers | 500 | 15 | |
| inventory_master | 180 | 8 | 30 products x 6 branches |
| stock_ledger | 237,230 | 9 | IN, OUT and ADJUSTMENT movements, 2019-01-01 to 2025-01-28 |
| sales_orders_header / lines | 20,000 / 130,402 | 11 / 9 | Orders from 2019-01-01 to 2024-12-31 |
| purchase_orders_header / lines | 24,000 / 155,495 | 10 / 9 | |
| invoices | 18,033 | 10 | One per delivered order |
| payments | 19,257 | 5 | |

## 2. What is healthy

- **No orphan keys.** All 15 parent-child relationships join cleanly (sales lines to orders, products, customers, branches; purchase lines to orders and suppliers; invoices to orders; payments to invoices; stock ledger and inventory to products and branches).
- **No duplicate rows** in any table, and no duplicate `so_id`, `po_id` or `movement_id`.
- **Order totals reconcile:** every sales order header total equals the sum of its lines.
- **No date conflicts:** no delivery date before its order date, and every date column parses correctly.
- **Stock ledger agrees with inventory:** the last running balance per product and branch equals `current_stock` (checked on a sample).
- Invoices (18,033) match delivered orders (18,033) exactly.

## 3. Data quality issues to fix in Week 2

| # | Issue | Table | Size | Planned fix |
|---|---|---|---|---|
| 1 | **Duplicate invoice IDs.** The same `invoice_id` is used for different sales orders (different customers and dates). | invoices | 197 rows | Decide on a rule: build a unique key (invoice_id + so_id) and flag affected payments. |
| 2 | **Duplicate payment IDs** | payments | 202 rows | Check whether they are true repeats or ID collisions, then dedupe or re-key. |
| 3 | **Invoices with no payment record** (roughly matches the 1,837 marked Unpaid) | invoices | 1,800 | Keep, label as unpaid, and reconcile status against payments. |
| 4 | **Overpaid invoices.** Payments sum to more than the invoice total. Likely caused by issue 1. | invoices / payments | 155 | Re-test after fixing issue 1. |
| 5 | **Very small payments** (below 100, against a median of about 820,000) | payments | 9 | Review as possible errors or test records. |
| 6 | **Current stock far above stock limits.** `current_stock` is 88,853 to 121,013, while `max_stock` is 131 to 582 and `opening_stock` is 80 to 300. Every inventory row exceeds its max. | inventory_master | 180 of 180 | Investigate whether stock is genuinely huge, quantities are on a different scale, or the ledger accumulates IN movements without enough OUT. Do not treat this as real overstock until explained. |
| 7 | **Missing received_date.** All blanks are cancelled purchase orders, so this is expected. | purchase_orders_header | 2,370 | Keep as null, and exclude cancelled orders from delivery-time analysis. |
| 8 | **Warehouse capacity stored as text** (for example "45230 sqft") | branches | 6 | Convert to a numeric column in square feet. |
| 9 | **Cancelled orders** (1,967 sales, 2,370 purchase) | headers | | Filter out before revenue and supplier metrics. |
| 10 | **Mixed unit scale across tables.** Prices run from 310 to 119,000 per unit and stock levels are inconsistent with order quantities (1-20 per sales line). | sales / inventory | | Confirm units with the Fields Documentation. |

## 4. Early questions and KPI ideas

- What share of purchase orders arrived on or before the expected delivery date, by supplier?
- Which products make up most of sales value (ABC / Pareto)?
- Which branches have the best revenue per employee and operating margin?
- How much invoice value is unpaid or overdue, by customer segment?
- Inventory turnover per product and branch, once the stock levels in issue 6 are explained.

## 5. Next step (Week 2)

Resolve issues 1, 2, 6 and 8 first, since they affect most later analysis. Then document every cleaning decision in a log.

# Transcation-Reconcilliation-and-Bussiness-Metrices-Summary
This document provides an overview of the automated transaction reconciliation process executed in this notebook, comparing an **Internal Ledger** against **Payment Gateway Data** to detect anomalies and extract key operational metrics.


# # Transaction Reconciliation & Business Metrics Summary



---

## 1. Executive Dashboard Metrics

Based on the analysis of **200 initial transactions**, the high-level financial health and system integrity metrics are as follows:

*   **Total GMV (Internal Ledger):** ‹709,343.00
*   **Confirmed GMV (Fully Matched):** ‹554,081.00
*   **Amount at Risk (Discrepancies):** ‹122,569.00
*   **Reconciliation Rate:** 91.0%
*   **Average Ticket Size:** ‹3,546.72
*   **Total Issues Identified:** 24

---

## 2. Identified Discrepancies & Severity

A total of **24 discrepancies** were identified and classified by severity level to prioritize operations:

### Discrepancy Breakdown by Type
1.  **MISSING_IN_GATEWAY (10 Cases - HIGH Severity):** Transactions recorded in our internal ledger but absent from the payment gateway records. This represents potential uncaptured revenue.
2.  **MISSING_IN_LEDGER (6 Cases - MEDIUM Severity):** Transactions recorded successfully at the gateway but not registered in our internal ledger system.
3.  **AMOUNT_MISMATCH (5 Cases - MEDIUM/HIGH Severity):** Discrepancies where ledger amounts do not align with gateway records, likely due to unexpected fee deductions or partial processing anomalies.
4.  **STATUS_MISMATCH (3 Cases - CRITICAL Severity):** Transactions where our ledger assumes success (`SUCCESS`) but the gateway reports a failure (`FAILED`). This represents a critical risk of delivering goods/services for failed payments.

### Discrepancy Severity Summary
*   **CRITICAL:** 3 issues (Status mismatches)
*   **HIGH:** 11 issues (Major amount discrepancies or missing gateway captures)
*   **MEDIUM:** 10 issues (Missing ledger entries or minor fee discrepancies)

---

## 3. Dimensions & Breakdown Analysis

The notebook aggregated and prepared the following dimensional breakdowns for visualization:
*   **Payment Method Breakdown:** Tracking transactional volume and GMV across *UPI*, *Card*, *NetBanking*, and *Wallet*.
*   **Merchant Category Breakdown:** Distribution of sales across *Electronics*, *Fashion*, *Food & Grocery*, *Travel*, and *Entertainment*.
*   **Regional Performance:** Sales performance tracking across *North*, *South*, *East*, *West*, and *Central* regions.
*   **Daily Trends:** Time-series aggregations tracking volume fluctuations and GMV day-over-day.

---

## 4. Exported Deliverables

To power downstream reporting and operational workflows, the following structured CSV datasets were generated and exported:

1.  `summary_metrics.csv` — Contains high-level dashboard metrics (reconciliation rate, total GMV, etc.).
2.  `daily_summary.csv` — Aggregated daily transactional counts and financial totals.
3.  `payment_method_breakdown.csv` — Volume and GMV metrics sliced by payment options.
4.  `category_breakdown.csv` — Performance metrics sliced by merchant categories.
5.  `region_breakdown.csv` — Sales performance mapped across geographical zones.
6.  `discrepancies.csv` — Complete line-item audit list detailing every unmatched transaction, its root cause, severity, and suggested action
   items.



**`Payment_Reconciliation_Analysis.ipynb`**

# Notebook Summary: Payment Reconciliation Analysis

This notebook demonstrates a complete end-to-end workflow for reconciling internal transaction ledgers against external payment gateway exports. It simulates typical discrepancies found in production environments and implements robust data-matching algorithms to detect them.

## 1. Data Generation & Setup
* **Internal Ledger Simulation (`internal_ledger`):** Generates 100 transaction records containing transaction IDs (`TXN01001` onwards), random typical e-commerce transaction amounts, randomized transaction timestamps spanning 3 days, and realistic payment method weights (UPI 50%, Card 30%, NetBanking 10%, Wallet 10%).
* **Gateway Export Simulation (`gateway_export`):** Created by modifying a copy of the internal ledger to introduce real-world anomalies.

## 2. Simulated Issues & Discrepancies
Four major data discrepancy issues are intentionally introduced into the dataset for analysis:
* **Issue 1 (Missing Transactions in Gateway):** 5% of transactions (5 records worth **₹7,795**) present in the ledger are removed from the gateway export.
* **Issue 2 (Amount Mismatch):** 3% of common transactions are modified with arbitrary processing fees deducted, leaving a discrepancy of **₹250** between the systems.
* **Issue 3 (Extra Transactions in Gateway):** 2 system-bug transactions (`TXN99901`, `TXN99902`) totaling **₹4,598** are added to the gateway but are absent from the internal ledger.
* **Issue 4 (Status Difference):** 2 transactions successfully logged in our internal ledger are changed to `FAILED` in the gateway dataset.

## 3. Discrepancy Reconciliation Engine
* Hand-coded step-by-step set operations and pandas merge/filtering processes are first utilized to isolate and clean up data for the report.
* All the findings are unified into a final **Reconciliation Report** sorted by absolute monetary impact.
* **Reusable Helper Function (`reconcile_payments`):** The notebook concludes by packing all the manual lookup logic into a highly flexible, reusable utility function. It takes any ledger and gateway DataFrame to automatically yield a detailed status dictionary containing issue counts, risk valuations, and filtered mismatch reports.

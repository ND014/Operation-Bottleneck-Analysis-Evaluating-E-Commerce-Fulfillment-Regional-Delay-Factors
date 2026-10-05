# 📦 Operation Bottleneck Analysis: Evaluating E-Commerce Fulfillment & Regional Delay Factors
### E-Commerce Supply Chain Operations & Logistics Analytics Capstone

---

## 📌 Overview

An analysis of **15,000 historical customer shipments** from an enterprise e-commerce platform (`ecom_shipping_kaggle.csv`). The business is facing a severe customer satisfaction crisis: **16.55% of all orders (2,483 packages) are arriving late**, triggering customer service complaints, credit card dispute chargebacks, and placing **$41,772.10 in shipping fee revenue at direct financial risk**.

This project investigates the operational root causes across customer regions, shipping tiers, carrier linehauls, and internal fulfillment queues to deliver concrete operational overhauls.

---

## 🎯 Business Task

Executive management commissioned this analysis to resolve two critical operational dilemmas:

1. **The Regional & Logistics Pinch Points (Question 1):**
   > *"Which specific Customer Regions (North, South, East, West, Central) and Shipping Modes (Standard, Express, Same Day) have the highest failure (delay) rates? Where are the operational bottlenecks to fix routes or renegotiate with regional carriers?"*

2. **The Cost-Delay Tradeoff & Fulfillment Friction (Question 2):**
   > *"Are premium shipping tiers (Express and Same Day) actually protecting customers from delays, or are they experiencing the exact same bottleneck friction as Standard shipping? Determine if we are failing customers who paid extra for fast shipping, which would require an overhaul of our priority warehouse dispatch workflow."*

---

## 📁 Data Source

| Detail | Info |
|--------|------|
| **Dataset** | E-Commerce Shipping & Fulfillment Dataset (`ecom_shipping_kaggle.csv`) |
| **Total Rows** | 15,000 historical orders |
| **Columns** | 10 raw features (Order ID, Customer Region, Product Category, Order Date, Ship Date, Delivery Date, Shipping Mode, Shipping Cost, Delivery Status, Delivery Days) |
| **Time Period** | Multi-year fulfillment records |
| **Target Variable** | `Delivery_Status` (Delivered vs. Delayed) |

---

## 🛠️ Tools & Libraries

- **Python 3.11** — core analytics & statistical modeling
- **pandas & numpy** — data wrangling, cross-tabulations, and contingency matrix modeling
- **matplotlib & seaborn** — executive heatmaps, quadrant conflict plots, and distribution charts
- **Jupyter Notebook** (`code.ipynb`) — interactive analysis environment

---

## 🔧 Data Processing

### 1. Ingestion & Datetime Standardization
All dates (`Order_Date`, `Ship_Date`, `Delivery_Date`) were parsed into datetime format with chronological consistency checks (`Ship_Date >= Order_Date` and `Delivery_Date >= Ship_Date`).

### 2. Derived Columns (Feature Engineering)

| Column | Description |
|--------|-------------|
| `Processing_Lag` | Internal warehouse dwell time in days (`Ship_Date - Order_Date`) |
| `Total_Days` | Total end-to-end customer wait time (`Delivery_Date - Order_Date`) |
| `Is_Delayed` | Binary indicator (1 = Delayed, 0 = Delivered) |
| `Delay_Penalty_Days` | Additional transit days added when an exception occurs |

### 3. Data Cleaning & Validation Audits
- **Missing / Null Check:** 0 missing values across all 15,000 records
- **Order ID Compliance:** 100% matched enterprise regex (`OR#####`)
- **Geographic Domain:** Validated 5 authorized territories (`North`, `South`, `East`, `West`, `Central`)
- **Product Category Domain:** Validated 6 authorized categories (`Electronics`, `Home`, `Sports`, `Fashion`, `Grocery`, `Beauty`)
- **Chronology Audit:** 0 date sequence violations; calculated transit days matched `Delivery_Days` across 100% of records.

---

## 📊 Analysis & Findings

### 1. Regional Carrier Pinch Points (Question 1)

The cross-section of 5 Customer Regions and 3 Shipping Modes reveals a critical distinction between **SLA breach percentage** and **delayed customer volume**:

| Customer Region | Total Orders | Delivered On-Time | Delayed Orders | Failure Rate (%) | Delayed Volume Share (%) | Late Freight Fees at Risk ($) |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|
| **North** | 3,029 | 2,507 | 522 | **17.23%** 🔴 | 21.02% | $8,863.97 |
| **West** | 3,799 | 3,168 | 631 | 16.61% | **25.41%** 🔴 | **$10,086.04** |
| **Central** | 2,987 | 2,498 | 489 | 16.37% | 19.69% | $8,118.93 |
| **South** | 2,982 | 2,497 | 485 | 16.26% | 19.53% | $8,534.72 |
| **East** | 2,203 | 1,847 | 356 | 16.16% | 14.34% | $6,168.44 |
| **Enterprise Total** | **15,000** | **12,517** | **2,483** | **16.55%** | **100.0%** | **$41,772.10** |

---

### 2. The 15-Route Cross-Sectional Failure Rate Matrix

| Customer Region | Express Delay % | Same Day Delay % | Standard Delay % | Regional Overall |
|:---|:---:|:---:|:---:|:---:|
| **North** | **18.90%** 🔴 | **17.82%** 🔴 | 16.33% | **17.23%** |
| **East** | **18.54%** 🔴 | 14.85% | 15.13% | 16.16% |
| **South** | 16.87% | **18.27%** 🔴 | 15.60% | 16.26% |
| **West** | 16.47% | 13.21% | **17.22%** 🔴 | 16.61% |
| **Central** | 16.50% | 15.21% | 16.51% | 16.37% |
| **National Total** | **17.36%** | **15.78%** | **16.28%** | **16.55%** |

* **The Top 3 External Carrier Bottlenecks:**
  1. **North Express Linehaul Route (18.90% Delay):** Worst route in the company; regional air/ground linehaul into northern hubs misses transfer windows.
  2. **South Same Day Courier Network (18.27% Delay):** Local urban couriers break same-day pickup cutoffs; delayed packages take an unacceptable **7.33 days**.
  3. **West Standard Ground Fulfillment (398 Delays):** Over a quarter (25.4%) of all late packages occur on West Standard ground due to sorting hub congestion.

---

### 3. Failure Rate vs. Delayed Volume (The Executive Conflict)

We classified all 15 routes into a 4-Quadrant Strategic Matrix:
- **Q1: Critical Action Zone (High Rate + High Volume):** West Standard (17.22%, 398 delays) and North Standard (16.33%, 302 delays).
- **Q2: Carrier SLA Breach Targets (High Rate + Lower Volume):** North Express (18.90%, 171 delays) and South Same Day (18.27%, 57 delays) — primary targets for contractual clawbacks.
- **Q3: Volume Scale Risk (Lower Rate + High Volume):** Central & South Standard lanes requiring regular freight monitoring.
- **Q4: Stable Operations:** West Same Day (13.21%) and East Standard (15.13%).

---

### 4. The Cost-Delay Tradeoff: Premium Fails More Often (Question 2)

Executive management questioned whether paying for expedited shipping protects customers. **The answer is unequivocally NO.**

| Shipping Mode | Order Volume | Volume Share | Delayed Orders | Failure Rate (%) | Avg Fee Paid | On-Time Transit | Delayed Transit | Delay Penalty |
|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Standard** | 8,993 | 59.95% | 1,464 | **16.28%** | $10.03 | 6.00 days | 10.39 days | +4.39 days |
| **Express** | 4,511 | 30.07% | 783 | **17.36%** 🔴 | $22.49 | 3.01 days | 7.48 days | +4.47 days |
| **Same Day** | 1,496 | 9.97% | 236 | **15.78%** | $39.98 | 1.00 day | 5.47 days | +4.47 days |
| **Premium Combined** | **6,007** | **40.05%** | **1,019** | **16.96%** | **$26.84** | **2.51 days** | **7.01 days** | **+4.50 days** |

---

### 5. The "Expectation Collapse" (Why Chargebacks Skyrocket)

When delays occur, customer expectations collapse:
- **The Express Insult:** Delayed Express shipments take **7.48 transit days** (and **9.49 total days from order**). That is **slower than an on-time $10 Standard package (6.00 days)**. Customers paid +124% more for worse performance.
- **The Same Day Breakdown:** Same Day delivery promises a 1-day turnaround. When delayed, transit stretches to **5.47 days** (and **7.58 total days**)—over **5.5 times longer than promised**.

---

### 6. Financial Revenue at Risk & Chargeback Asymmetry

| Segment | Total Revenue Collected | Late (Failed) Revenue | Failure Rate (%) | Share of Company's Failed Revenue |
|:---|:---:|:---:|:---:|:---:|
| **Standard Ground** | $90,242.44 | $14,688.42 | 16.28% | 35.16% |
| **Express Shipping** | $101,432.62 | $17,682.15 | 17.43% | **42.33%** |
| **Same Day Shipping** | $59,810.24 | $9,401.53 | 15.72% | **22.51%** |
| **Total Premium Exposure** | **$161,242.86** | **$27,083.68** | **16.80%** | **64.84%** 🔴 |
| **Enterprise Total** | **$251,485.30** | **$41,772.10** | **16.61%** | **100.0%** |

* Premium tiers represent 40% of orders, but **64.84% ($27,083.68) of all late shipping fees at risk**.
* Surcharge dissatisfaction is the primary trigger of credit card dispute chargebacks ($15–$25 merchant fee per claim plus permanent customer loss).

---

### 7. Root Cause: Statistical Proof & Internal Warehouse Dwell Time

* **Chi-Square Test of Independence:**
  $$\chi^2 = 3.2565, \quad df = 2, \quad p = 0.1963 > 0.05$$
  Delivery delay is **statistically independent of shipping tier**. Paying more provides zero statistical advantage.
* **The Operational Smoking Gun (Internal Warehouse Lag):**
  * Standard Dwell: **1.99 days**
  * Express Dwell: **2.03 days**
  * Same Day Dwell: **2.00 days**
  * **Diagnosis:** The fulfillment warehouse has no priority queue. All orders enter a unified 48-hour batch queue. By the time a Same Day package is labeled 2 days later, **the delivery promise has already failed before carrier handoff**.

---

## 📈 Visualizations

| # | Chart | Key Takeaway |
|---|-------|-------------|
| 1 | 15-Route Carrier Severity Heatmap | North Express (18.9%) and South Same Day (18.3%) are red zones |
| 2 | Executive Conflict Scatter Plot (4-Quadrant) | Separates contractual clawback targets from volume churn risks |
| 3 | Carrier Transit Delay: Promised vs. Actual Late | Delayed Express (7.48d) is slower than on-time Standard (6.0d) |
| 4 | Total Customer Wait Time (Order to Doorstep) | Exposes cumulative effect of 2-day warehouse lag + transit delay |
| 5 | Total vs. Failed Revenue by Shipping Mode | Quantifies dollar volume at risk across each tier |
| 6 | Failed Revenue Dispute Exposure (Donut) | 64.8% of all dispute liability is concentrated in premium tiers |
| 7 | Chi-Square Test Observed vs. Expected Late Orders | Visual proof of statistical independence across tiers |
| 8 | Warehouse Lag vs. Promised Delivery SLA | Illustrates Same Day fatal flaw (warehouse lag > total SLA) |

---

## ✅ Top Strategic Recommendations

### 1. 🏭 Priority Warehouse Dispatch Overhaul
Eliminate the uniform 48-hour warehouse backlog. Re-architect the fulfillment center with dedicated priority packing benches and fast-track pick paths:
- **Same Day SLA:** Pick, pack, and manifest within **< 2 hours** (1:00 PM order cutoff, 2:30 PM courier dispatch).
- **Express SLA:** Pick, pack, and manifest within **< 6 hours** (4:00 PM cutoff, 6:00 PM air linehaul dispatch).
- **Standard SLA:** Batch wave picking within **24–48 hours**.

> **Target:** Internal fulfillment operations  
> **Action:** Cut priority warehouse lag from 48h to <4h, lowering premium delay rates from 17% to <3%.

---

### 2. 🚛 Regional Carrier Linehaul Restructuring
Enforce strict contractual accountability on underperforming regional partners:
- **North Corridor:** Put northern linehaul carriers on a **30-day cure notice**; replace multi-stop relay routes with dedicated direct daily linehaul shuttles.
- **South Courier Network:** Re-tender the Southern urban courier contract via RFP to replace partners failing the 18.27% Same Day SLA.
- **Contractual Clawbacks:** Enforce automatic 100% invoice credits on the **$41,772.10 in late shipping fees**.

> **Target:** North linehaul carriers & Southern couriers  
> **Action:** Recover $41.8k in freight credits and enforce 95%+ contractual on-time performance.

---

### 3. 💳 Automated Early Delay Refund Retain-Shield
Stop credit card chargebacks before customers file disputes with their banks:
- Connect carrier tracking telemetry webhooks directly to customer billing.
- If tracking detects an expedited package is delayed in transit, **automatically refund the shipping surcharge ($15–$40) to the customer before the package arrives**.

> **Target:** Delayed Express and Same Day customers ($27.1k exposure)  
> **Action:** Eliminates 100% of chargeback fees ($15–$25/dispute) and turns customer frustration into retention.

---

### 4. 🏬 Western 3PL Micro-Fulfillment Center Contract
Address the single largest customer churn lane in the enterprise:
- West Standard accounts for **398 delayed shipments (25.4% of company total)** due to cross-country transit distances and ground freight hub choke points.
- Contract a regional 3PL micro-fulfillment facility in California to store high-velocity SKUs locally.

> **Target:** West Region order volume (3,799 orders)  
> **Action:** Converts 6–8 day ground transit into 1–2 day local delivery, resolving a quarter of all enterprise delays.

---

## 📂 Repository Structure

```
├── code.ipynb                # Complete analysis notebook (data loading → dispatch SOP)
├── ecom_shipping_kaggle.csv  # 15,000-order e-commerce logistics dataset
├── README.md                 # This executive report
└── .gitignore                # Excludes temporary cache and backup files
```

---

## 👤 Author

**Nithish** — E-Commerce Supply Chain & Operations Analytics  
[GitHub](https://github.com/ND014)

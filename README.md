# 💰 Finance Analysis Power BI Dashboard

An end-to-end **Power BI** project that transforms raw banking/transaction data into an interactive finance analytics dashboard — covering data cleaning, data modeling, DAX measures (including Year-over-Year comparisons), dynamic field parameters, and drill-through navigation.

---

## 📌 Project Overview

This dashboard analyzes financial transaction data (deposits, withdrawals, loan EMIs, card/bill payments, transfers, investments, etc.) across customers, regions, and time. It answers questions like:

- What is the total transaction amount, fee, and tax collected — and how does it compare to the previous year?
- How are transactions trending month over month?
- Which transaction statuses (Success / Failed / Pending) are driving fees?
- How do fees break down by customer segment (Retail, Premium, SME, Corporate, Wealth) and by state?
- Which transaction types (Loan EMI, Deposit, Transfer, etc.) generate the most amount, fees, and tax?
- What does the fee split look like by gender?
- Drill-through to the full, filterable transaction-level detail table.

---

## 📁 Dataset

| File | Rows | Description |
|---|---|---|
| `customers__1_.csv` | 5,000 | Customer master data |
| `finance_transactions.csv` | 50,069 | Transaction-level financial data |

### `customers__1_.csv`

| Column | Description |
|---|---|
| `customer_id` | Unique customer identifier (e.g. `C00001`) |
| `fisrt_name` | Customer's first name *(note: column is misspelled in the source file)* |
| `second_name` | Customer's last name |
| `gender` | Male / Female |
| `date_of_birth` | Date of birth (`DD-MM-YYYY`) |
| `city` | Customer's city |
| `state` | Customer's state |
| `occupation` | e.g. Salaried, Retired, Self-Employed |
| `customer_segment` | Retail, Premium, SME, Corporate, Wealth |
| `annual_income` | Annual income (numeric) |
| `join_date` | Date customer joined (`DD-MM-YYYY`) |


### `finance_transactions.csv`

| Column | Description |
|---|---|
| `transaction_id` | Unique transaction identifier (e.g. `T00000001`) |
| `transaction_date` | Date of transaction (`DD-MM-YYYY`) |
| `account_id` | Account identifier |
| `customer_id` | Foreign key → `customers.customer_id` |
| `transaction_type` | Bill Payment, Card Payment, Deposit, Fee Charge, Interest Credit, Investment, Loan EMI, Refund, Transfer, Withdrawal |
| `channel` | e.g. Mobile App, UPI, Net Banking |
| `merchant_category` | e.g. Groceries, Food Delivery |
| `amount` | Transaction amount |
| `fee_amount` | Fee charged on the transaction |
| `tax_amount` | Tax charged on the transaction |
| `currency` | Currency code (e.g. INR) |
| `transaction_status` | Success / Failed / Pending |
| `is_fraud` | Yes / No flag |
| `risk_score` | Numeric risk score |
| `reference_no` | Reference number for the transaction |



### 🔗 Relationship
`finance_transactions.customer_id` → `customers.customer_id` (Many-to-One)

### ⚠️ Data Quality Notes
- The `fisrt_name` column in `customers__1_.csv` contains a typo in the source file (should be `first_name`) — kept as-is here to match the raw data, but renamed to `first_name` during Power Query cleaning.
- Date columns (`date_of_birth`, `join_date`, `transaction_date`) are stored as text in `DD-MM-YYYY` format and were converted to proper Date types during transformation.

---

## 🗂️ Data Model

The model follows a **star schema** with one fact table and supporting dimension/parameter tables, built and validated in Power BI's Model View.

| Table | Type | Description |
|---|---|---|
| `finance_transactions` | Fact | Core transaction-level data |
| `customers` | Dimension | Customer master data |
| `Calendar Table` | Date Dimension | Marked as the official Date Table |
| `Dynamic Metric` | Parameter (disconnected) | Field Parameter used to switch KPI/metric dynamically |

### Relationships
- `finance_transactions[customer_id]` **→** `customers[customer_id]` — Many-to-One
- `finance_transactions[transaction_date]` **→** `Calendar Table[Date]` — Many-to-One (Calendar Table marked as Date Table)
- `Dynamic Metric` is a disconnected **Field Parameter** table used with `SELECTEDVALUE()` to let users switch the metric shown in visuals/cards from a single slicer.

### Table: `Calendar Table` (Date Dimension)
`Date`, `Month`, `Month NO`, `Year`, `Title` — a standard calendar table used for time intelligence and marked as the model's official date table.

### Table: `Dynamic Metric` (Field Parameter)
Fields: `Dynamic Metric`, `Dynamic Metric Fields`, `Dynamic Metric Order` — used to let the user toggle the headline metric (e.g. Total Amount, Total Fees, Total Tax, Total Transaction) via a single slicer button instead of building separate visuals for each KPI.

---

## 🧹 Data Cleaning & Transformation (Power Query)

- Fixed the misspelled `fisrt_name` column and standardized/combined customer name fields.
- Converted text-based date fields (`transaction_date`, `date_of_birth`, `join_date`) to proper Date types.
- Standardized categorical fields (`transaction_type`, `transaction_status`, `customer_segment`, `gender`, `state`).
- Removed/handled nulls and inconsistent formatting in numeric fields (`amount`, `fee_amount`, `tax_amount`).
- Built a dedicated `Calendar Table` and marked it as the Date Table for accurate time intelligence.
- Created relationships between `finance_transactions`, `customers`, and `Calendar Table`.

---

## 📐 DAX Measures

Key measures built in the model (grouped by purpose):

**Core KPIs**
- `Total Amount` — `SUM(finance_transactions[amount])`
- `Total Fees` — `SUM(finance_transactions[fee_amount])`
- `Total Tax` — `SUM(finance_transactions[tax_amount])`
- `Total Transaction` — `COUNT(finance_transactions[transaction_id])`
- `Avg Transaction Value` — `AVERAGE(finance_transactions[amount])`

**Prior Year / Year-over-Year Intelligence**
- `PY Amount`, `PY Fees`, `PY Tax`, `PY Transaction`, `PY Average Transaction` — prior-year values via `SAMEPERIODLASTYEAR()` / `CALCULATE()` + `Calendar Table`
- `YOY Amount`, `YOY Fees`, `YOY Tax`, `YOY Transaction`, `YOY Average` — current vs. prior year variance
- `YOY % Amount`, `YOY % Fees`, `YOY % Tax`, `YOY % Transaction`, `YOY % Average` — variance as a percentage (shown as "vs Previous Year" on the KPI cards)

**Dynamic Metric / Field Parameter Support**
- `Selected Dynamic Metric`, `Selected year` — helper measures used with `SELECTEDVALUE()` to drive the field-parameter-based KPI card and title
- `Dynamic Title` — dynamic card/visual title that updates based on the selected metric

> 💡 Replace the formulas above with your exact DAX syntax when you finalize the file — the measure **names** shown here were reverse-engineered from the Model View field list.

---

## 📊 Report Pages

### 1️⃣ Overview Analysis
- **KPI Cards:** Total Amount, Total Transaction, Avg Transaction Value, Total Fees, Total Tax — each with a YoY % vs. previous year indicator
- **Total Amount by Month** — line/area chart of monthly transaction volume
- **Total Fees by Transaction Status** — donut chart (Success / Failed / Pending)
- **Total Fees by Customer Segment** — bar chart (Retail, Premium, SME, Corporate, Wealth)
- **Fees by State** — bar chart across states (Maharashtra, Karnataka, Gujarat, Tamil Nadu, Uttar Pradesh, Madhya Pradesh)
- **Transaction Type Analysis** — table breaking down Amount, Fees, Tax, and Transaction count by transaction type
- **Total Fees by Gender** — donut chart (Female / Male)
- **Slicers:** Year, Dynamic Metric (field parameter), Occupation, Category

### 2️⃣ Transaction (Drill-through / Detail Page)
A detailed, filterable transaction table with:
`Transaction Id`, `Customer Name`, `Transaction Date`, `Transaction Type`, `Transaction Status`, `Gender`, `Customer Segment`, `State`, `Total Amount`, `Total Fees`, `Total Tax`

Configured as a **drill-through page** so users can right-click a data point on the Overview Analysis page (e.g., a customer segment, state, or transaction type) and jump straight to the underlying transaction-level records.

---

## ✨ Key Features

- ✅ Clean star-schema data model with a dedicated Date Table
- ✅ Custom DAX measures for KPIs and Year-over-Year comparisons
- ✅ **Dynamic Metric field parameter** — one slicer swaps the headline KPI across visuals
- ✅ **Drill-through** from summary visuals to transaction-level detail
- ✅ Interactive slicers: Year, Metric, Occupation, Category
- ✅ Multi-dimensional breakdowns: by transaction type, status, segment, state, and gender

---

## 🛠️ Tech Stack
- **Power BI Desktop** — data modeling, DAX, and report design
- **Power Query (M)** — data cleaning and transformation
- **DAX** — measures, KPIs, time intelligence

---

## 🚀 How to Use
1. Clone this repository.
2. Open `Finance Analysis Dashboard.pbix` in Power BI Desktop.
3. If prompted, update the data source connection to point to the CSVs (`customers__1_.csv`, `finance_transactions.csv`) in this repo.
4. Click **Refresh** to load the latest data.
5. Use the **Year**, **Dynamic Metric**, **Occupation**, and **Category** slicers on the Overview Analysis page to explore the data.
6. Right-click any chart element and choose **Drill through → Transaction** to view detailed records.

---

## 📷 Screenshots

| Overview Analysis | Transaction Detail |
|---|---|
| *(add screenshot path here)* | *(add screenshot path here)* |

| Data Model View |
|---|
| *(add screenshot path here)* |

---

## 📄 License
Specify your license here (e.g., MIT).

## 🙋 Author
Add your name / LinkedIn / portfolio link here.

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

**Sample row:**

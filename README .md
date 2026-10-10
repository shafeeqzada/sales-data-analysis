# Cleaning & Analysing a Dirty Financial Transactions Dataset (pandas)

A hands-on data-cleaning and exploratory-analysis project using a deliberately messy dataset of 100,000 financial transactions. The goal: turn unreliable raw data into a trustworthy dataset, document every decision, and extract business insights.

**Tools:** Python, pandas, NumPy, Matplotlib, Jupyter Notebook

## Dataset

`dirty_financial_transactions.csv` - 100,000 rows, 8 columns:
`Transaction_ID`, `Transaction_Date`, `Customer_ID`, `Product_Name`, `Quantity`, `Price`, `Payment_Method`, `Transaction_Status`

## Data Quality Problems Found

| Column | Problem | How I handled it |
|---|---|---|
| All | ~1,000 exact duplicate rows | Removed with `drop_duplicates()` |
| Transaction_ID | ~5,000 missing, ~940 repeated IDs | Dropped rows with no ID; kept first occurrence of repeated IDs |
| Transaction_Date | Missing values and ~63,000 impossible dates (`2023-13-01`, `2025-02-30`) | Converted with `errors="coerce"` so invalid dates became `NaT`. Not dropped (would lose ~68% of data) and not filled (would invent dates). Time-based analysis uses valid dates only (28,410 rows) |
| Customer_ID | ~4,600 missing | Dropped rows with a missing Customer_ID |
| Product_Name | Truncated names (`Lapt`, `Tabl`, `Smartph`, ...) | Mapped to the 5 real products by prefix matching |
| Quantity | Negative and missing values | Negatives set to NaN, then missing values filled with the median |
| Price | Stored as text with `$` signs; negative and missing values | Stripped `$`, converted to float, negatives set to NaN, missing filled with the median. Added a `Price_Imputed` flag column to keep track of filled values |
| Payment_Method | Inconsistent spellings (`creditcard`, `credit card`, `pay pal`, ...) | Normalised to 3 categories |
| Transaction_Status | `complete` / `completed` / `Completed`, plus ~15,000 missing | Standardised spelling; missing labelled `Unknown` |

**Result:** 100,000 rows -> 89,442 clean rows.

## Key Assumptions & Limitations

- A large share of `Price` values (the `Price_Imputed` rows) were filled with a single median, so revenue totals are partly estimated. Price-level statistics should use only non-imputed rows.
- `Unknown` statuses and customers are excluded when calculating failure rates and customer rankings.
- Only ~32% of rows have a valid date, so trend analysis reflects that subset.

## Analysis & Findings

1. **Revenue by product:** Revenue is spread almost evenly across all five products (about 2.0-2.04 billion each), with no clear leader.
2. **Payment methods:** Credit Card (43.1%) and PayPal (42.7%) are used almost equally; Cash is far less common (14.2%). Average transaction value is almost the same across methods.
3. **Failed-transaction rate (known statuses only):** Among 74,562 rows with a known status, 59.9% were completed, 20.1% failed and 20.0% were pending, so about one in five transactions failed.
4. **Top customers by revenue:** C2023 is the top customer (about 6.69 million), followed by C643, C915, C2544 and C1590 (5.13 to 5.66 million).
5. **Monthly and yearly revenue trend (valid dates only):** Revenue is flat with no clear growth or decline (about 42 to 62 million per month, 616 to 642 million per year from 2020 to 2024). December 2024 is the highest month and 2022 the highest year. The low values for February 2025 and 2025 reflect incomplete data.
6. **Failures by product and payment method:** Failures follow transaction volume, so no product or payment method fails noticeably more often than the others.

## Repository Structure

```
├── dirty_financial_transactions.csv   # raw data
├── cleaned_transactions.csv           # output of the notebook
├── Sales_Data_Analysis.ipynb          # cleaning + analysis
└── README.md
```

## How to Run

```bash
pip install pandas numpy matplotlib jupyter
jupyter notebook Sales_Data_Analysis.ipynb
```

## What I Learned

- Handling text-formatted numbers, invalid dates and inconsistent categories
- Choosing between dropping, filling and flagging missing data, and documenting the trade-offs
- Avoiding misleading statistics by excluding unknown values from rate calculations

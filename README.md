# Supermarket Sales Analysis

Exploratory data analysis of 500 supermarket transactions recorded across four
Indian branches between 1 January and 1 July 2026. The project validates the raw
transaction data, answers six operational questions about products, branches,
categories, payments, customers and ratings, and converts each answer into a
business decision.

---

## Project description

A four-branch supermarket chain captures every till transaction but does not use
those records to make decisions. Stock is ordered on intuition, the membership
programme has never been evaluated, and branch performance differences have never
been explained.

This analysis works through the full pipeline — load, validate, engineer, aggregate,
visualise, interpret — and produces answers to six questions:

| # | Question | Answer |
|---|---|---|
| 1 | Which product generates the highest sales? | **Cheese** — ₹27,906.30 (11.4% of revenue) |
| 2 | Which branch performs best? | **Branch C, Mumbai** — ₹72,469.45 (29.6%) |
| 3 | Which category sells the most? | **Beverages** — ₹56,108.24 |
| 4 | What is the most popular payment method? | **UPI** — 127 of 500 transactions (25.4%) |
| 5 | Do members spend more than normal customers? | **No** — ₹483.14 vs ₹497.07 per transaction |
| 6 | What is the average customer rating? | **3.99 out of 5** |

Headline totals: **₹2,44,885.34** across **500 transactions** and **2,756 units**,
at an average sale of **₹489.77**.

### The finding worth the attention

Question 5 is the only result that contradicts expectation, and it is the most
actionable. Members do not spend more per visit than non-members. A Welch's t-test
returns **p = 0.724**, so the ₹13.93 difference is not statistically significant —
the two groups are behaving identically. A membership programme built on a flat
discount produces exactly this outcome, because it reduces the value of baskets that
would have been bought anyway. Replacing it with a spend threshold would give
members a reason to add to the basket rather than subtract from the bill.

---

## Dataset

**File:** `supermarket_sales_500_rows.csv` (included in this repository)

**Source:** [SUPER MARKET DATA — Google Sheets](https://docs.google.com/spreadsheets/d/1QIX__4VObHFMEXnRM2xJyXmB5JAB2peHrJcQ41_U9TE/edit?usp=sharing)

500 rows × 13 columns, no missing values, no duplicate invoices.

| Column | Type | Description |
|---|---|---|
| `Invoice ID` | text | Unique transaction identifier (INV0001–INV0500) |
| `Date` | date | Transaction date, 2026-01-01 to 2026-07-01 |
| `Branch` | text | Branch code: A, B, C or D |
| `City` | text | Jaipur, Delhi, Mumbai or Bengaluru |
| `Customer Type` | text | Member or Normal |
| `Gender` | text | Male or Female |
| `Product` | text | One of 19 products |
| `Category` | text | One of 8 categories |
| `Quantity` | integer | Units sold, 1–10 |
| `Unit Price` | float | Price per unit in ₹ |
| `Payment` | text | UPI, Card, Cash or Net Banking |
| `Rating` | float | Customer rating, 1.0–5.0 |
| `Sales` | float | `Quantity × Unit Price`, in ₹ |

The notebook recomputes `Sales` from `Quantity × Unit Price` and asserts it matches
the stored column on all 500 rows before any analysis runs.

---

## Technologies used

| Tool | Role |
|---|---|
| **Python 3.11+** | Language |
| **pandas** | Loading, cleaning, grouping, pivot tables |
| **NumPy** | Numeric arrays and histogram bin edges |
| **Matplotlib** | Bar, line, pie, histogram and dual-axis charts |
| **seaborn** | Box plot, heatmap, chart theme |
| **SciPy** | Welch's t-test for the member vs normal comparison |
| **Jupyter Notebook** | Analysis environment |
| **Power BI Desktop** | Optional dashboard layer (measures listed below) |

---

## Setup and run

### 1. Clone or download

```bash
git clone <your-repository-url>
cd supermarket-sales-analysis
```

### 2. Create a virtual environment

```bash
python -m venv venv

# macOS / Linux
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the notebook

```bash
jupyter notebook Vanisree_SupermarketSalesAnalysis.ipynb
```

Then run all cells: **Kernel → Restart & Run All**.

Keep `supermarket_sales_500_rows.csv` in the same folder as the notebook, or edit
the `DATA_PATH` variable in the setup cell to point at it.

### Running without Jupyter

```bash
jupyter nbconvert --to script Vanisree_SupermarketSalesAnalysis.ipynb
python Vanisree_SupermarketSalesAnalysis.py
```

---

## Repository contents

```
.
├── Vanisree_SupermarketSalesAnalysis.ipynb   Full analysis, all cells executed
├── Vanisree_ProjectReport.docx               Written project report
├── supermarket_sales_500_rows.csv            Dataset, 500 transactions
├── requirements.txt                          Python dependencies
└── README.md                                 This file
```

---

## Notebook structure

1. **Problem statement** — what the business needs to know
2. **Dataset** — schema and column descriptions
3. **Setup** — imports and chart styling
4. **Load the data** — read CSV, parse dates, inspect
5. **Data quality checks** — missing values, duplicates, category values, range
   checks, and verification that `Sales = Quantity × Unit Price`
6. **Feature engineering** — ordered `Month` column, `Branch Label`
7. **Analysis** — the six questions, each with a table and a chart
8. **Summary of findings** — one consolidated results table
9. **Business decisions** — five recommendations drawn from the results
10. **Conclusion** — what the analysis established, and its limitations

---

## Key findings in detail

**Revenue concentrates in high-value, low-frequency items.** Cheese tops revenue on
25 transactions while Noodles sells on 43 and contributes half as much. These need
different stocking logic — Cheese protects margin, Soap and Noodles drive footfall.

**Branch performance is structural, not service-driven.** Mumbai is 52.3% ahead of
Jaipur on revenue but does not lead on customer rating, so the gap comes from basket
composition, catchment or layout rather than service quality.

**Payment methods are close to evenly split.** UPI leads at 25.4%, but the four
methods sit within two percentage points of each other. No rail can be deprioritised.

**Ratings are spread, not clustered.** The distribution is roughly uniform between
3.0 and 5.0 rather than bunched at the top, which makes 3.99 a mediocre score. A
substantial minority of visits are unsatisfactory, and service quality is the only
lever here that costs neither stock nor margin.

---

## Optional: Power BI dashboard

The same CSV drives a Power BI report. Load the file, set Date to Date type and
Quantity to Whole Number, add a Calendar table, then create these measures:

```dax
Total Sales           = SUM ( 'Sales'[Sales] )
Transactions          = COUNTROWS ( 'Sales' )
Units Sold            = SUM ( 'Sales'[Quantity] )
Avg Transaction Value = DIVIDE ( [Total Sales], [Transactions] )
Avg Rating            = AVERAGE ( 'Sales'[Rating] )
Member Avg Sale       = CALCULATE ( [Avg Transaction Value], 'Sales'[Customer Type] = "Member" )
Normal Avg Sale       = CALCULATE ( [Avg Transaction Value], 'Sales'[Customer Type] = "Normal" )
Branch Share %        = DIVIDE ( [Total Sales], CALCULATE ( [Total Sales], ALL ( 'Sales'[Branch] ) ) )
```

Build it out with card visuals for the five headline measures, a line chart on
`Calendar[Month]`, bar charts for branch and product (Top 10 filter), a stacked
column for branch × category, a donut for payment method, and slicers for Branch,
Category, Customer Type, Payment and Month.

---

## Limitations

- Six months of data with no prior year, so seasonality cannot be separated from noise.
- No cost or margin data — "highest selling" means highest revenue, not most profitable.
- No customer identifier, so repeat visits and lifetime value cannot be measured.
- Ratings are unlabelled as to what they score (service, product or store), which
  limits how far the service-quality conclusion can be pushed.

---

## Author

**Vani Sree** — Data Analytics Project, 2026

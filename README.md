# 🛍️ Customer Shopping Behavior Analysis

> End-to-end analysis of 3,900 customer transactions — Python · MySQL · Power BI

---

## 📌 Overview

This project analyzes customer shopping behavior using transactional data from **3,900 purchases** across multiple product categories. The goal was to uncover patterns in spending, customer loyalty, product performance, and subscription behavior — and turn them into clear business recommendations.

The workflow covers the full analytics pipeline: cleaning and preparing data in Python, loading it into a **MySQL** database, answering 10 business questions with SQL, and visualizing the results in an interactive **Power BI** dashboard.

---

## 📂 Dataset

| Attribute | Details |
|---|---|
| **Rows** | 3,900 |
| **Columns** | 18 |
| **Demographics** | Age, Gender, Location, Subscription Status |
| **Purchase details** | Item Purchased, Category, Purchase Amount, Season, Size, Color |
| **Behavior** | Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type |
| **Missing data** | 37 missing values in `Review Rating` |

---

## 🛠️ Tools & Technologies

| Category | Tool |
|---|---|
| Language | Python (pandas) |
| Database | MySQL (via SQLAlchemy + PyMySQL) |
| BI Dashboard | Power BI |
| Notebook | Jupyter Notebook |
| Report | PDF |

---

## 🔄 Project Workflow

### 1. Data Loading
Imported the raw CSV into a pandas DataFrame and inspected its structure using `df.info()` and `df.describe(include='all')`.

### 2. Data Cleaning
- **Missing values:** Filled 37 missing `Review Rating` values using the **median rating of each product category** (`groupby('Category').transform`), rather than a flat average — preserving category-level rating differences.
- **Column standardization:** Converted all column names to snake_case (e.g. `Purchase Amount (USD)` → `purchase_amount`) for cleaner SQL queries and documentation.
- **Redundant column check:** Verified that `discount_applied` and `promo_code_used` were identical across all rows, then dropped `promo_code_used` to avoid duplication.

### 3. Feature Engineering
- **`age_group`** — created using quartile-based binning (`pd.qcut`) into Young Adult, Adult, Middle-aged, and Senior segments.
- **`purchase_frequency_days`** — mapped categorical frequency labels (e.g. "Weekly", "Monthly", "Quarterly") to numeric day values for easier aggregation and analysis.

### 4. Database Integration
Connected to a local **MySQL** instance using SQLAlchemy and loaded the cleaned DataFrame into a `customer` table with `df.to_sql()`, making it ready for SQL-based analysis.

### 5. SQL Analysis (10 Business Questions)
Wrote and executed structured queries — including aggregations, subqueries, `CASE` statements, CTEs, and window functions (`ROW_NUMBER() OVER PARTITION BY`) — to answer real business questions (see results below).

### 6. Power BI Dashboard
Built an interactive dashboard with KPI cards, slicers, and category/age-group breakdowns to make the SQL findings explorable for non-technical stakeholders.

### 7. Report & Recommendations
Compiled methodology, results, and business recommendations into a final PDF report.

---

## 🧮 SQL Analysis & Key Results

| # | Business Question | Key Finding |
|---|---|---|
| 1 | Revenue: Male vs. Female | Male customers generated **$157,890** vs. **$75,191** from female customers — more than double |
| 2 | High-spending discount users | **839 customers** used a discount yet still spent above the average purchase amount |
| 3 | Top 5 products by review rating | Gloves (3.86), Sandals (3.84), Boots (3.82), Hat (3.80), Skirt (3.78) |
| 4 | Standard vs. Express shipping spend | Nearly identical: **$58.46** (Standard) vs. **$60.48** (Express) — shipping speed isn't a major spend driver |
| 5 | Subscribers vs. non-subscribers | Non-subscribers drive **far more total revenue** ($170,436 vs $62,645), though avg. spend per customer is similar (~$59–60) — subscriber base is simply smaller (1,053 vs 2,847) |
| 6 | Most discount-dependent products | Hat (50%), Sneakers (49.7%), Coat (49.1%), Sweater (48.2%), Pants (47.4%) of purchases involved a discount |
| 7 | Customer segmentation | **Loyal: 3,116** · Returning: 701 · New: 83 — an overwhelmingly loyal customer base |
| 8 | Top 3 products per category | E.g. Clothing → Blouse, Pants, Shirt · Footwear → Sandals, Shoes, Sneakers · Outerwear → Jacket, Coat |
| 9 | Repeat buyers & subscription link | Of customers with 5+ previous purchases, **2,518 are non-subscribers** vs. only 958 subscribers — repeat buyers don't necessarily subscribe |
| 10 | Revenue by age group | Young Adult ($62,143) leads, followed closely by Middle-aged, Adult, and Senior — revenue is fairly evenly spread across age groups |

> Full query logic is available in [`customer_behavior_sql_queries.sql`](./customer_behavior_sql_queries.sql).

---

## 📊 Power BI Dashboard

The dashboard brings the SQL findings to life with interactive filtering by subscription status, gender, category, and shipping type.

**KPI cards:** 3.9K customers · $59.76 avg. purchase amount · 3.75 avg. review rating

**Visuals included:**
- Customer split by subscription status (donut chart)
- Revenue and sales by category (bar charts)
- Revenue and sales by age group (horizontal bar charts)
- Full slicer panel for ad-hoc filtering

> 📁 Dashboard screenshot available in the project report — see `Customer_Shopping_Behavior_Analysis.pdf`.

---

## 💡 Business Recommendations

- **Boost subscriptions** — promote exclusive perks, since non-subscribers currently make up 73% of customers and the bulk of revenue
- **Strengthen loyalty programs** — reward the large "Loyal" segment (3,116 customers) and create pathways to convert "Returning" customers
- **Review discount policy** — top discount-dependent products (Hat, Sneakers, Coat) may be over-discounted; balance sales volume against margin
- **Lead with top-rated products** — feature Gloves, Sandals, and Boots in marketing given their consistently high ratings
- **Target high-revenue segments** — focus campaigns on Young Adults and high-spending discount users (the 839 customers who spend above average despite discounts)

---

## 🚀 How to Run

### Prerequisites
```bash
pip install pandas sqlalchemy pymysql
```

### Step 1 — Run the Notebook
Open and run `Customer_Shopping_Behavior_Analysis.ipynb` to load, clean, and engineer features on the dataset.

### Step 2 — Load Data into MySQL
The notebook's final cells connect to MySQL and push the cleaned DataFrame into a `customer` table:
```python
from sqlalchemy import create_engine

engine = create_engine("mysql+pymysql://<username>:<password>@localhost:3306/customer_behavior")
df.to_sql("customer", engine, if_exists="replace", index=False)
```
> ⚠️ Replace the placeholder credentials with your own local MySQL setup before running.

### Step 3 — Run SQL Queries
Execute the queries in [`customer_behavior_sql_queries.sql`](./customer_behavior_sql_queries.sql) against the `customer` table to reproduce all 10 business question results.

### Step 4 — Explore the Dashboard & Report
Open the Power BI dashboard file (if included) or refer to `Customer_Shopping_Behavior_Analysis.pdf` for the full visual report and write-up.

---

## 📁 Repository Structure

```
📦 customer-shopping-behavior-analysis/
├── 📓 Customer_Shopping_Behavior_Analysis.ipynb   # Data loading, cleaning, feature engineering
├── 🗄️ customer_behavior_sql_queries.sql            # 10 business-question SQL queries
├── 📄 Customer_Shopping_Behavior_Analysis.pdf      # Full report with dashboard & recommendations
└── 📝 README.md
```

---

## 👤 Author

**Ashutosh Kumar**
Analyst — Data Analytics & Financial Insights
[LinkedIn](https://www.linkedin.com/in/ashutoshkumar21) · [HackerRank](https://www.hackerrank.com/ashutosh1kumar11)

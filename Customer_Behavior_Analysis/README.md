# Customer Shopping Behavior Analysis — Python, SQL & Power BI

End-to-end analysis of a retail customer dataset (3,900 transactions, 18 attributes) covering the full workflow: data cleaning and feature engineering in Python, loading into SQL Server, business analysis through SQL, and an interactive Power BI dashboard for stakeholders.

---

## Business Problem

A retail company noticed shifting purchase patterns across demographics, product categories, and channels. Management wanted to understand what actually drives buying decisions and repeat purchases — discounts, review ratings, seasonality, shipping options, or payment preferences.

The core question:

> How can the company use shopping data to identify trends, improve customer engagement, and optimise marketing and product strategy?

---

## Tech Stack

| Stage | Tools |
|---|---|
| Data cleaning & feature engineering | Python (Pandas, NumPy), Jupyter Notebook |
| Database & querying | SQL Server (SSMS), SQLAlchemy, pyodbc |
| Visualisation | Power BI (DAX, Power Query) |

---

## Dataset

3,900 customer transactions with 18 columns covering demographics (age, gender, location), product details (item, category, size, colour, season), transaction data (purchase amount, payment method, shipping type), and behavioural signals (review rating, subscription status, discount usage, previous purchases, purchase frequency).

---

## What I Did

### 1. Data Preparation — Python

- Profiled the dataset using `.info()`, `.describe()` and null checks to establish baseline data quality.
- Found 37 missing values in `review_rating`. Rather than dropping rows or using a global mean, I filled them with the **median rating within each product category** — ratings vary meaningfully by category, so a category-level median preserves that signal.
- Standardised all column names to lowercase with underscores for clean SQL compatibility.
- Engineered `age_group` using quartile binning (`pd.qcut`) into Young Adult, Adult, Middle-aged and Senior — giving four balanced cohorts instead of arbitrary age brackets.
- Engineered `purchase_frequency_days` by mapping the text field `frequency_of_purchases` to numeric day intervals (Weekly → 7, Fortnightly → 14, Quarterly → 90, etc.), converting a categorical field into something measurable.
- Verified `discount_applied` and `promo_code_used` were identical across all 3,900 rows, then dropped the redundant column.
- Loaded the cleaned dataframe into SQL Server via SQLAlchemy with a trusted connection.

### 2. Business Analysis — SQL

Ten business questions answered using SQL Server, covering:

- Revenue split by gender and by age group
- Customers who used a discount yet still spent above the overall average (subquery)
- Top 5 products by average review rating
- Average spend comparison across shipping types
- Subscriber vs non-subscriber spending behaviour
- Products with the highest discount penetration rate
- **Customer segmentation** into New / Returning / Loyal using a `CASE` statement inside a CTE
- **Top 3 products per category** using `ROW_NUMBER() OVER (PARTITION BY ...)`
- Overlap between repeat buyers and subscription status

Queries use joins, aggregations, subqueries, CTEs, window functions and conditional aggregation.

### 3. Dashboard — Power BI

Single-page interactive dashboard connected to the SQL Server database.

**KPI cards** built with DAX measures — Number of Customers, Average Purchase Amount, Average Review Rating.

**Visuals:**
- Revenue and customer count by product category (clustered column)
- Revenue and customer count by age group (clustered bar)
- Subscription status split (donut)

**Slicers** for subscription status, gender, category and shipping type — allowing stakeholders to filter the entire report and answer their own follow-up questions without going back to the analyst.

---

## Key Findings

**Revenue and scale**
- Total revenue across the dataset: **$233,081** from 3,900 transactions, averaging **$59.76** per purchase.

**Loyal customers carry the business**
- Customers with more than 10 previous purchases (Loyal segment) account for **80% of total revenue** — $185,517 of $233,081 — from 3,116 of 3,900 customers. New customers contribute just **2%**. Retention is doing far more work here than acquisition.

**Discounts aren't lifting basket size**
- 43% of all transactions used a discount, yet discounted purchases averaged **$59.28** against **$60.13** for non-discounted ones. The discount is being applied without a corresponding increase in spend — worth reviewing whether it's driving incremental purchases or just reducing margin on sales that would have happened anyway.

**Subscription isn't changing spend behaviour**
- Only 27% of customers are subscribed, and subscribers average **$59.49** per purchase versus **$59.87** for non-subscribers. The programme currently isn't shifting purchase value, suggesting the benefits may need rethinking.

**Category concentration**
- Clothing ($104,264) and Accessories ($74,200) together drive **77% of revenue**. Outerwear is the smallest at $18,524.

**Spend is evenly distributed across demographics**
- Revenue is remarkably flat across the four age groups (Young Adult $62,143 down to Senior $55,763) and across seasons (Fall $60,018 to Summer $55,777). There's no single dominant demographic — the customer base is broad rather than concentrated.

---

## Recommendations

1. **Audit the discount programme.** Discounted transactions don't spend more. Test targeted discounts on lapsed or Returning-segment customers instead of broad application.
2. **Rework the subscription offer.** At 27% adoption with no measurable spend lift, the current value proposition isn't landing. Consider tying benefits to spend thresholds or exclusive access.
3. **Protect the Loyal segment.** With 80% of revenue concentrated in repeat buyers, retention risk is the biggest exposure. Prioritise loyalty rewards and early access over broad acquisition spend.
4. **Build a New → Returning conversion path.** Only 83 customers sit in the New segment. The gap between first and second purchase is where the funnel is leaking.
5. **Lean into Clothing and Accessories** for inventory and campaign investment, and reassess whether Outerwear justifies its shelf space.

---

## Repository Structure
├── customer_shopping_behavior.csv # Raw dataset (3,900 rows)
├── customer_shopping_behavior_analysis.ipynb # Python: cleaning, feature engineering, SQL load
├── customer_behavior_sql_queries.sql # 10 business questions in SQL
├── customer_behavior_dashboard.pbix # Power BI dashboard
├── Business_Problem_Document.pdf # Original brief
└── README.md


---

## How to Run

1. Clone the repo and open `customer_shopping_behavior_analysis.ipynb`.
2. Run the notebook to clean the data and engineer features.
3. Create a database named `customer_behavior` in SQL Server, then run the final notebook cell to load the cleaned dataframe into a `customer` table.
4. Open `customer_behavior_sql_queries.sql` in SSMS and run the analysis queries.
5. Open `customer_behavior_dashboard.pbix` in Power BI Desktop and point the connection at your local SQL Server instance.

**Requirements:** Python 3.8+, `pandas`, `sqlalchemy`, `pyodbc`, ODBC Driver 18 for SQL Server, SQL Server, Power BI Desktop.

---

## About

Built by **Akash Navale** — Data Analyst working across BI, dashboard development and customer analytics.

[LinkedIn](https://www.linkedin.com/in/akash-navale-903ab218b) · [GitHub](https://github.com/AkashNavale-BI)

# 🛍️ Customer Shopping Behavior Analysis

**End-to-end data analytics project** covering data cleaning (Python), business-question SQL analysis (PostgreSQL), and an interactive Power BI dashboard — built on 3,900 retail transactions.

![Python](https://img.shields.io/badge/Python-Pandas-blue?logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-PostgreSQL-336791?logo=postgresql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)

---

## 📌 Project Overview

A retail company wants to understand what actually drives its customers' purchase decisions — discounts, reviews, seasonality, payment method, subscriptions — so it can improve sales, engagement, and long-term loyalty.

This project answers that using the full analytics stack:

1. **Python** — cleans and engineers the raw dataset
2. **SQL (PostgreSQL)** — answers 10 targeted business questions
3. **Power BI** — turns the findings into an interactive dashboard
4. **Report & recommendations** — translates the analysis into stakeholder actions

> **Business question:** *How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?*

---

## 🗂️ Dataset

| | |
|---|---|
| **Rows** | 3,900 transactions |
| **Columns** | 18 |
| **Fields** | Demographics (age, gender, location), purchase details (item, category, amount, season, size, color), and behavior (discount applied, previous purchases, purchase frequency, review rating, subscription status, shipping type, payment method) |
| **Data quality** | 37 missing values in `Review Rating`, imputed using the median rating per product category |

---

## 🧹 Data Preparation (Python)

Cleaning and feature engineering was done in `Customer_Shopping_Behavior_Analysis.ipynb`:

- **Missing values** — imputed `Review Rating` nulls with the median rating for that product's category (rather than a global median, to preserve category-level rating patterns)
- **Column standardization** — renamed all columns to snake_case for consistency across Python/SQL
- **Feature engineering:**
  - `age_group` — customers binned into quartiles (Young Adult / Adult / Middle-aged / Senior)
  - `purchase_frequency_days` — converted categorical frequency labels (e.g. "Fortnightly", "Quarterly") into a numeric day count for easier aggregation
- **Redundancy check** — confirmed `discount_applied` and `promo_code_used` were perfectly correlated and dropped the duplicate column
- **Database load** — loaded the cleaned DataFrame into PostgreSQL via SQLAlchemy for structured SQL analysis (connection code for MySQL and SQL Server is also included as an alternative)

---

## 🗃️ SQL Analysis

Ten business questions were answered in `customer_behavior_sql_queries.sql` using joins, aggregations, `CASE` statements, subqueries, and window functions (`ROW_NUMBER() OVER PARTITION BY`):

| # | Business Question | SQL Technique |
|---|---|---|
| 1 | Total revenue: male vs. female customers | `GROUP BY`, `SUM` |
| 2 | Customers who used a discount but still spent above average | Correlated subquery |
| 3 | Top 5 products by average review rating | `GROUP BY`, `ORDER BY` |
| 4 | Standard vs. Express shipping — average purchase amount | `GROUP BY`, `AVG` |
| 5 | Subscribers vs. non-subscribers — spend and revenue | `GROUP BY`, `COUNT`/`AVG`/`SUM` |
| 6 | Top 5 products with highest discount-purchase rate | Conditional aggregation (`CASE WHEN`) |
| 7 | Customer segmentation — New / Returning / Loyal | `CASE`, CTE |
| 8 | Top 3 best-selling products per category | Window function (`ROW_NUMBER() OVER PARTITION BY`) |
| 9 | Do repeat buyers (5+ purchases) subscribe more? | Filtered `GROUP BY` |
| 10 | Revenue contribution by age group | `GROUP BY`, `SUM` |

<details>
<summary>Example query — top 3 products per category (window function)</summary>

```sql
WITH item_counts AS (
    SELECT category,
           item_purchased,
           COUNT(customer_id) AS total_orders,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY COUNT(customer_id) DESC) AS item_rank
    FROM customer
    GROUP BY category, item_purchased
)
SELECT item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
```
</details>

---

## 📊 Power BI Dashboard

An interactive dashboard (`customer_behavior_dashboard.pbix`) visualizes the SQL findings — revenue by segment, discount impact, category performance, and subscription behavior — so stakeholders can explore the results without touching code.

> 📷 *Add a screenshot or GIF of the dashboard here — this is the single highest-impact addition you can make to this README. Export a PNG from Power BI (File → Export → Image) and embed it with `![Dashboard](assets/dashboard.png)`.*

---

## 💡 Key Findings & Business Recommendations

- **Boost subscriptions** — subscribers show different spend patterns than non-subscribers; promoting exclusive subscriber benefits is a clear lever
- **Build loyalty programs** — reward repeat buyers to move them from "Returning" into the high-value "Loyal" segment
- **Rebalance discount strategy** — a small set of products are highly discount-dependent; balance the sales lift against margin impact
- **Double down on top performers** — highlight top-rated and best-selling products (per category) in campaigns rather than spreading marketing evenly
- **Target high-value segments** — focus marketing spend on the highest-revenue age groups and express-shipping customers, who show distinct purchasing behavior

---

## 🛠️ Tech Stack

- **Python** — pandas (cleaning, feature engineering)
- **SQL** — PostgreSQL (via SQLAlchemy + psycopg2)
- **Power BI** — dashboard & visualization
- **Jupyter Notebook** — analysis workflow

---

## 📁 Repository Structure

```
├── Business Problem Document.pdf              # Original business brief
├── Customer_Shopping_Behavior_Analysis.ipynb   # Data cleaning & feature engineering
├── customer_behavior_sql_queries.sql           # 10 business-question SQL queries
├── customer_behavior_dashboard.pbix            # Power BI dashboard
├── customer_shopping_behavior.csv              # Raw dataset
├── Customer Shopping Behavior Analysis.pdf     # Full project report
├── Customer-Shopping-Behavior-Analysis.pptx    # Stakeholder presentation
└── README.md
```

---

## 🚀 How to Reproduce

1. **Clone the repo**
   ```bash
   git clone <your-repo-url>
   cd customer-trends-data-analysis-SQL-Python-PowerBI
   ```
2. **Install dependencies**
   ```bash
   pip install pandas sqlalchemy psycopg2-binary
   ```
3. **Run the notebook** — `Customer_Shopping_Behavior_Analysis.ipynb` to clean the data and load it into PostgreSQL (create a local database named `customer_behavior` first, or point it at your own instance)
4. **Run the SQL queries** — execute `customer_behavior_sql_queries.sql` against the loaded `customer` table in your PostgreSQL client of choice
5. **Open the dashboard** — `customer_behavior_dashboard.pbix` in Power BI Desktop

> ⚠️ **Before pushing publicly:** the notebook's database-connection cells contain a placeholder database password. Swap these for environment variables (e.g. `os.environ["DB_PASSWORD"]`) before sharing the repo, so no credentials — even placeholder ones — sit in version control.

---

## 📬 Contact

If you have questions about the analysis or want to discuss the approach, feel free to reach out via [LinkedIn](https://www.linkedin.com/in/sunny-chaudhary-61811b306/) or [email](sunnychaudhary1029@gmail.com).

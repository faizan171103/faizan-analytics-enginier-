# Mohd Faizanul Haque - Analytics Engineer Portfolio

![SQL](https://img.shields.io/badge/SQL-4479A1?style=flat&logo=postgresql&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![dbt](https://img.shields.io/badge/dbt-FF694B?style=flat&logo=dbt&logoColor=white)
![Snowflake](https://img.shields.io/badge/Snowflake-29B5E8?style=flat&logo=snowflake&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=flat&logo=databricks&logoColor=white)
![DuckDB](https://img.shields.io/badge/DuckDB-FFF000?style=flat&logo=duckdb&logoColor=black)
![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=flat&logo=powerbi&logoColor=black)

**Analytics Engineer | Data Modeling | Data Quality | Business Intelligence**
New Delhi, India

---

## About

I'm an Analytics Engineer who owns the full path from raw source to business metric: ingesting data, structuring it in layered warehouses, testing it, modeling it for BI, and then analyzing it to produce findings a stakeholder can act on. My toolkit spans SQL, Python, dbt, Snowflake, Databricks, DuckDB, and Power BI, developed through a Data Analytics internship and a series of end-to-end projects covering marketing, e-commerce, hospitality, retail, and customer behavior.

I ask the same question at every layer of a project: **if a stakeholder built a decision on this number, would it hold up, and could I explain why?** In practice that means:

- Separating concerns by layer, so each one can be debugged and rebuilt on its own.
- Writing dbt tests that fail the build when data breaks a rule, instead of shipping a bad number.
- Keeping business logic (segments, ROI formulas) out of the final model, so a redefinition doesn't ripple through everything.
- Flagging inconsistencies between dashboard pages rather than quietly correcting them.
- Backing every recommendation with a quantified number.

---

## Table of Contents

- [About](#about)
- [Core Competencies](#core-competencies)
- [Portfolio Projects](#portfolio-projects)
  - [Marketing Analytics Engineering](#marketing-analytics-engineering) - HubSpot API · Python · DuckDB · dbt · Power BI
  - [Olist E-Commerce Analytics Platform](#olist-e-commerce-analytics-platform) - Databricks · Unity Catalog · dbt · PySpark · Power BI
  - [Snowflake Bookings Analytics](#snowflake-bookings-analytics) - Snowflake · SQL · Power BI
  - [Additional Analytics Projects](#additional-analytics-projects)
- [Skills](#skills)
- [Experience](#experience)
- [Education](#education)
- [Contact](#contact)

---

## Core Competencies

| Area | What I do |
|---|---|
| **Ingestion** | Pull data from APIs and files with authentication, pagination, and validation before it reaches the warehouse |
| **Warehouse Architecture** | Design Bronze / Silver / Gold (and staging / intermediate / marts) layers where each layer has one job |
| **Transformation** | Build modular, reusable SQL and dbt models for cleaning, standardization, and business logic |
| **Dimensional Modeling** | Model star schemas with clear grain, fact tables, and dimensions that BI tools can query directly |
| **Data Quality** | Enforce uniqueness, not-null, accepted-value, and relationship tests that run on every build |
| **BI Enablement** | Point Power BI at governed Gold tables so every dashboard page answers from the same source |
| **Analysis** | Turn modeled data into ranked, quantified business recommendations |

---

## Portfolio Projects

## Marketing Analytics Engineering

**Goal:** Build an end-to-end analytics engineering pipeline that takes customer data from the HubSpot API, transforms it through a layered dbt model on DuckDB, and delivers a single trustworthy fact table to Power BI.

**Code:** [View Repository](https://github.com/faizan171103/marketing-analytics-engineering)

**Description:** Most practice projects stop at loading a CSV into a dashboard. This one was built to mirror a real marketing analytics stack: a live API source instead of a static file, a layered warehouse so business logic isn't buried in one giant query, and a single well-defined fact table at the end, because BI tools are only as trustworthy as the grain of the table they point at.

The pipeline is deliberately linear and one-directional: nothing downstream writes back upstream. If a number in Power BI looks wrong, there is exactly one path to walk backwards to find where it broke.

**Pipeline Layers:**

| Layer | Responsibility |
|---|---|
| **Ingestion** | A single Python entry point (`ingestion/run_ingestion.py`) authenticates against HubSpot, pulls contact and customer records, and validates them before anything touches the warehouse, so downstream dbt tests check business logic rather than malformed API responses |
| **Raw** | A faithful landing zone for HubSpot fields. No renaming, no logic, so there is always an unmodified source of truth to rebuild from |
| **Staging** | Renames fields to consistent conventions, fixes data types, and standardizes values. This layer absorbs HubSpot-specific quirks so nothing downstream has to know them |
| **Intermediate** | `int_customer_metrics` holds the business logic: customer tenure, revenue per visit, marketing ROI, support ticket rate, email engagement rate, and customer status and value segments |
| **Gold (Marts)** | `fct_customer_analytics` is the contract with Power BI, with one row per customer and fields (revenue, engagement, churn flag, segment) a dashboard can use without another join or `CASE WHEN` |

**What I Built:**

- Designed a Python ingestion step with authentication and validation against the HubSpot API.
- Built a staging, intermediate, and marts dbt project on DuckDB with a clear single responsibility per layer.
- Isolated segmentation rules and ROI formulas in the intermediate layer so business redefinitions don't ripple into Gold.
- Modeled a one-row-per-customer fact table, `fct_customer_analytics`, as the only source Power BI reads from.
- Implemented dbt tests for required fields, accepted values, `customer_id` uniqueness, and relationships between layers, so a broken assumption fails `dbt build` instead of shipping a bad number.
- Managed the environment with `uv`, kept secrets out of the repo through `.env.example` and `profiles.yml.example`, and generated dbt docs for lineage.
- Organized the Power BI report around four business questions: Executive Overview, Acquisition Analytics, Customer Analytics, and Engagement Analytics.

**Key Analytics:**

- Customer tenure and lifecycle status
- Revenue per visit and marketing ROI
- Email engagement rate
- Support ticket rate
- Customer value segmentation
- Churn flagging
- Acquisition channel quality

**Documented Next Steps:**

- Incremental models, since everything currently rebuilds in full.
- Snapshots to track history on slowly changing attributes such as `customer_value_segment`.
- Orchestration, so ingestion and dbt run on a schedule instead of as separate manual steps.

**Skills:** SQL, Python, API Ingestion, Data Validation, ETL/ELT, Data Modeling, Data Quality, dbt Testing, Business Intelligence, Data Visualization

**Technology:** HubSpot API, Python, DuckDB, dbt, Power BI, uv, Git/GitHub

---

## Olist E-Commerce Analytics Platform

**Goal:** Build an end-to-end analytics engineering platform that transforms nine raw e-commerce source tables into a governed, business-ready dimensional model for reporting and decision-making.

**Code:** [View Repository](https://github.com/faizan171103/olist-databricks-lakehouse)

**Description:** This project uses the Olist Brazilian E-Commerce dataset on Databricks with Unity Catalog and dbt, following the Medallion Architecture (Bronze, Silver, Gold). It keeps three concerns separate that are easy to tangle together: fidelity to source (Bronze), correctness of types and values (Silver), and business meaning (Gold).

Raw tables land in Bronze untouched, so the source of truth stays reprocessable. Silver staging models standardize types, handle nulls, normalize strings, and filter invalid records. Gold models shape the cleaned data into a star schema that Power BI and ad hoc SQL users can query through a single join-friendly interface.

**Platform at a Glance:**

| Metric | Value |
|---|---|
| Total Revenue | R$13.59M |
| Total Orders | 99K |
| Total Customers | 96K |
| Average Order Value | R$137.75 |
| Repeat Customer Rate | 3.05% |
| Average Review Score | 4.09 / 5 |
| Product Categories | 74 |

**What I Built:**

- Designed a Bronze → Silver → Gold lakehouse pipeline in Databricks with Unity Catalog governance.
- Built reusable dbt staging models (`stg_orders`, `stg_products`, `stg_customers`, and others) with explicit type casting, null handling, and invalid-record filtering.
- Modeled a star schema with fact tables for orders, order items, payments, and reviews, and dimensions for customer, product, seller, and date.
- Implemented dbt tests for uniqueness on `customer_id`, `order_id`, `product_id`, and `seller_id`, and accepted-value checks on `order_status` and `review_score`, run on every `dbt build`.
- Connected the Gold layer to Power BI for Overview, Sales, Customer, and Product dashboards.
- Identified a semantic inconsistency between two dashboard pages: the Sales page category breakdown totals R$13.59M with `health_beauty` on top, while the Product page donut totals R$1.33M with `bed_bath_table` on top. I documented it as an open item and recommended a shared Power BI semantic layer instead of page-level filters.

**Key Findings:**

- **Retention is the biggest lever.** Repeat customers are only 3.05% of the base (2.91K of 96K), so a modest retention gain would move revenue more than any acquisition optimization.
- **Payment is card-driven.** Credit card accounts for 73.9% of transactions and boleto 19.0%, and most orders are paid in a single installment against a modest R$137.75 AOV.
- **Revenue is a long tail.** The top category, `health_beauty`, holds only 9.26% of revenue, so no single category is a point of failure.
- **Volume and value live in different cities.** São Paulo and Rio de Janeiro lead on customer count, but the highest spend per customer comes from smaller cities.
- **Revenue peaks and drops sharply.** Revenue climbed to a May peak (~R$1.5M) and fell to ~R$0.6M in September, a pattern that appears in both revenue and order volume, which points to a demand or supply event rather than a pricing anomaly.
- **One-off category spike.** `audio` spiked to ~R$150K in August and collapsed immediately after, a sign of a one-off promotion or event.

**Recommendations:**

1. Prioritize retention programs, because that is the highest-leverage number on the platform.
2. Test installment options on high-ticket categories such as `computers` and `furniture_decor` to lift AOV without hurting conversion on cheaper items.
3. Cross-sell bundles (for example `bed_bath_table` with `housewares`) rather than trying to grow a single hero category.
4. Investigate the September drop with delivery and inventory data, which are not yet in the Gold model.

**Skills:** SQL, Python, PySpark, Data Cleaning, Data Validation, ETL/ELT, Data Warehousing, Dimensional Modeling, Data Quality, Business Intelligence, Data Visualization

**Technology:** Databricks, Unity Catalog, dbt, Spark/PySpark, Power BI, Medallion Architecture (Bronze, Silver, Gold)

---

## Snowflake Bookings Analytics

**Goal:** Build an end-to-end data engineering and analytics pipeline that transforms raw hotel booking CSVs into clean, validated, business-ready datasets for revenue, occupancy, and operational reporting.

**Code:** [View Repository](https://github.com/faizan171103/snowflake_bookings_analytics)

**Description:** This project is built on Snowflake using the Medallion Architecture. Raw booking CSVs are loaded into Bronze exactly as received. The Silver layer then targets specific, observed failure modes rather than running a generic "clean the data" pass. The Gold layer produces analytics-ready tables that connect directly to Power BI.

**Silver Layer: Targeted Fixes**

| Issue | Handling |
|---|---|
| Invalid or malformed dates | Parsed against expected formats; unparseable rows flagged, not dropped |
| Inconsistent booking status values | Standardized to a controlled vocabulary (Confirmed, Cancelled, No-Show) |
| Malformed email addresses | Validated against a format check; invalid entries flagged |
| Inconsistent text fields (city, room type) | Trimmed, case-normalized, and deduplicated against known variants |
| Mixed or incorrect data types | Explicitly cast so Gold models never inherit ambiguous typing |

**Gold Layer: Three Tables, Three Jobs**

- **Booking fact table:** one row per booking, the grain everything else rolls up from.
- **Daily booking summary:** pre-aggregated for revenue trend and volume reporting.
- **City-level revenue aggregation:** pre-aggregated so Power BI doesn't re-scan the fact table for every geographic cut.

**Platform at a Glance:**

| Metric | Value |
|---|---|
| Total Revenue | $395.39K |
| Total Bookings | 1,187 |
| Total Guests | 3,440 |
| Average Booking Value | $332.26 |
| Revenue per Guest | $114.84 |
| Confirmed / Cancelled / No-Show | 41.5% / 31.1% / 27.1% |

**What I Built:**

- Designed a Bronze → Silver → Gold pipeline in Snowflake with independently rebuildable layers.
- Built reusable SQL transformations for date parsing, status standardization, email validation, text normalization, and type casting.
- Flagged invalid records instead of silently dropping them, so data loss stays visible.
- Created a booking fact table and two pre-aggregated Gold tables for BI performance.
- Connected Snowflake to Power BI for Hotel Booking and Revenue Analysis dashboards.
- Surfaced a data-quality gap: a `(Blank)` segment appears in both `BOOKING_STATUS` and `ROOM_TYPE`. I recommended tracing it back to Silver to determine whether it is a source gap or a transformation dropping unrecognized values, and surfacing it as a data-quality metric.

**Key Findings:**

- **Booking failure is the largest problem.** Only 494 of 1,187 bookings (41.5%) are Confirmed; Cancelled and No-Show together make up a 58.2% failure rate, roughly $230K in unrealized revenue at the current average booking value.
- **No premium tier exists.** The top 10 bookings cap at $600 each, so growth is entirely volume-driven.
- **Room demand is nearly even.** Suite 34.5%, Standard 33.5%, Deluxe 31.9%, which leaves room for upsell messaging to shift the mix.
- **Revenue is a long tail across many small markets.** Even the top city is under 0.5% of total revenue.
- **Revenue is volatile.** Monthly revenue swings between roughly $30K and $41K, peaking in May and October, which looks campaign-driven rather than seasonal.

**Recommendations, Ranked by Impact:**

1. Reduce cancellations and no-shows through deposits, automated reminders, and a tiered cancellation policy. Recovering 10 points is worth an estimated ~$40K.
2. Introduce bundled premium packages priced above the $332 average.
3. Reverse-engineer the May and October peaks and diagnose the June, July, and September troughs.
4. Concentrate marketing and inventory in proven cities.
5. Close the `(Blank)` data-quality gap before further segmentation.

**Skills:** SQL, Data Cleaning, Data Validation, ETL, Data Transformation, Data Warehousing, Data Modeling, Data Quality, Business Intelligence, Data Visualization

**Technology:** Snowflake SQL, Power BI, CSV Data Ingestion, Medallion Architecture (Bronze, Silver, Gold)

---

## Additional Analytics Projects

### Sales Analytics

Analyzed 64,000+ sales records across five years using Python and Pandas, then built an interactive Power BI dashboard. Identified recurring May–June revenue peaks and January slowdowns, and turned regional, channel, and product findings into recommendations for inventory, pricing, and marketing.

**Technology:** Python, Pandas, NumPy, Matplotlib, Seaborn, Excel, Power BI

**Links:** [Python Analysis](#) · [Cleaned Dataset](#) · [Repository](#)

### Analysis of Customer Behavior

Analyzed 3,900+ customer transactions using Python, SQL (CTEs, subqueries, window functions, `CASE` statements), and Power BI. Segmented customers into New, Returning, and Loyal groups and examined subscription behavior, discount usage, shipping preferences, and revenue by age group.

**Technology:** Python, Pandas, SQL, Power BI, Jupyter Notebook

**Links:** [SQL Analysis](#) · [Python Analysis](#) · [Repository](#)

> Replace each `#` above with your real file or repository link.

---

## Skills

| Category | Tools |
|---|---|
| **Languages & Query** | SQL, Python, Pandas, NumPy, PySpark |
| **Platforms** | Snowflake, Databricks, Unity Catalog, DuckDB |
| **Transformation & Modeling** | dbt, ETL/ELT, Medallion Architecture, Dimensional / Star Schema Modeling, Data Warehousing |
| **Data Quality** | dbt Tests, Data Validation, Data Cleaning, Data Contracts |
| **Ingestion** | REST API Integration (HubSpot), CSV Ingestion |
| **Visualization** | Power BI, Excel, Matplotlib, Seaborn |
| **Analytics** | Exploratory Data Analysis, Customer Segmentation, KPI Reporting, Business Analysis |
| **Tooling** | Git / GitHub, uv |

---

## Experience

### Full-Stack Developer Intern (Data Analytics)
**MetaCyrus.tech** | New Delhi, India | July 2024 – September 2024

- Performed data handling, preprocessing, cleaning, and validation to improve dataset accuracy and reliability.
- Organized and transformed datasets to support reporting workflows and data-driven decision-making.
- Developed reports and dashboards to identify business trends, performance metrics, and operational insights.
- Collaborated with cross-functional teams to understand requirements and deliver analytical solutions.
- Prepared structured datasets and improved the usability of reporting information.

---

## Education

**Guru Gobind Singh Indraprastha University**
Bachelor of Technology in Computer Science | New Delhi, India
Graduated: July 2026 | CGPA: 7.9

---

## Contact

- 📧 **Email:** mdf860111@gmail.com
- 💻 **GitHub:** [@faizan171103](https://github.com/faizan171103)
- 📍 **Location:** New Delhi, India

---
tags:
  - database
  - data_warehouse
summary: OLTP vs. OLAP, star/snowflake/wide table/Data Vault, ETL/ELT pipeline
use_cases:
  - etl
  - feature_engineering
---

# Data Warehousing

## What is a Data Model

A data model defines:
- Structure: how data is organized
- Relationships: how entities connect
- Constraints: rules ensuring data correctness

> Data modeling = trade-off between consistency, performance, and flexibility

## OLTP vs. OLAP

### OLTP (Online Transaction Processing)

**Purpose:**
Run the business

**Characteristics:**
- High concurrency (many small reads/writes)
- Strong consistency ([[ACID|ACID]])
- Normalized schema ([[Relational Database|3NF]])
- Point queries (by primary key)

**Example:**
```sql
User(id, name)
Order(id, user_id)
Payment(id, order_id)
```

> Keep data correct, consistent, and non-redundant

### OLAP (Online Analytical Processing)

**Purpose:**
Analyze the business

**Characteristics:**
- Read-heavy, write-light
- Large scans + aggregations (SUM, GROUP BY)
- Denormalized schema
- Complex queries across large datasets

> Make queries fast, even with redundancy

## OLAP Data Modeling

### Star Schema

**Structure:**
- Fact table (measures, events)
- Dimension tables (context)

**Example:**
```sql
-- Fact table
Fact_Sales(order_id, user_id, product_id, amount, time_id)

-- Dimension tables
Dim_User(user_id, city)
Dim_Product(product_id, category)
Dim_Time(time_id, day, month)
```

**Advantages:**
- Simple structure
- Fewer joins
- Fast aggregations

**Trade-off:**
- Some redundancy in dimensions

### Snowflake Schema

**Extension of star schema:**
- Normalize dimension tables

**Example:**
```sql
-- Fact table
Fact_Sales(order_id, user_id, product_id, amount, time_id)

-- Dimension tables
Dim_User(user_id, city_id)
Dim_City(city_id, city_name)

Dim_Product(product_id, category_id)
Dim_Category(category_id, category_name)

Dim_Time(time_id, day, month_id)
Dim_Month(month_id, month)
```

**Advantages:**
- Less redundancy
- Better data integrity (centralized data + foreign keys reduce duplication and inconsistency).

**Trade-off:**
- More joins -> slower queries

### Wide Table (Denormalized Table)

**Structure:**
- Flatten fact + dimensions into one table

**Example:**
```sql
Wide_Table(
  user_id,
  city,
  product_category,
  amount,
  day
)
```

**Advantages:**
- Minimal joins
- Extremely fast queries

**Use cases:**
- Feature engineering (ML)
- Real-time dashboards
- Risk systems

**Trade-off:**
- High redundancy
- Complex data updates

### Data Vault

Designed for enterprise data warehouses

**Core components:**
- Hub: business keys (entities)
```text
User: user_id  
Product: product_id
```

- Link: relationships
```text
User_Order: user_id, order_id
```

- Satellite: attributes + history
```text
User_SAT: user_id, name, city, load_time
```

**Key ideas:**
- Separate structure from attributes
- Track full history
- Enable schema evolution

**Advantages:**
- Highly scalable (append-only writes + distributed tables enable parallel scaling)
- Easy to extend (add new tables instead of altering existing schema)
- Strong auditability (append-only + timestamps + lineage enable full history tracking)

**Trade-off:**
- Complex design
- Not query-friendly (data is highly normalized and distributed across many tables, requiring complex joins, so a transformation layer is needed)

## Transactional Database vs. Data Warehouse

| Aspect           | Transactional Database            | Data Warehouse                   |
| ---------------- | --------------------------------- | -------------------------------- |
| Purpose          | Run business operations           | Analyze business data            |
| Workload         | ==OLTP== (transactions)           | ==OLAP== (analytics)             |
| Data             | Current, up-to-date               | Historical, accumulated          |
| Schema Design    | Normalized (3NF)                  | Denormalized (star / wide table) |
| Queries          | Point queries (PK/index lookup)   | Large scans + aggregations       |
| Updates          | Frequent INSERT / UPDATE / DELETE | Mostly append-only               |
| Storage          | Row-based                         | Columnar (often)                 |
| Performance Goal | Low latency                       | High throughput                  |

## ETL (Extract, Transform, Load)

**Core Idea**
> ETL moves data from OLTP systems into a data warehouse for analysis, enabling OLAP analysis.

### 1. Extract

- Pull data from source systems (OLTP databases, logs, APIs)
- Data is usually raw and heterogeneous

**Examples:**
- MySQL / PostgreSQL tables
- Application logs
- External APIs

### 2. Transform

- Clean, standardize, and reshape data

**Common operations:**
- Data cleaning (remove duplicates, handle nulls)
- Format normalization (e.g., date, currency)
- Joins and aggregations
- Mapping to target schema (star / wide table)

Example:
```sql
-- join and aggregate
SELECT user_id, SUM(amount) AS total_spent
FROM orders
GROUP BY user_id;
```

### 3. Load

- Write transformed data into the data warehouse

Modes:
- Batch load (hourly / daily)
- Incremental load (only new data)
- Full load (overwrite)

### ETL vs. ELT

- **ETL**: transform before loading  
  ```
  Source → Transform → Data Warehouse
  ```

- **ELT**: load first, then transform inside warehouse  
  ```
  Source → Data Warehouse → Transform
  ```

> Modern systems often use ELT (leveraging warehouse compute power)
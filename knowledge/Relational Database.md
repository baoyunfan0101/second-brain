---
tags:
  - database
  - transactions
  - sql
summary: constraints (Primary Key/Foreign Key/Unique/Not Null), normalization (1NF/2NF/3NF)
use_cases:
  - oltp
  - database_transactions
---

# Relational Database

## Overview

A relational database stores structured data in tables (relations) with predefined schemas. It is based on the relational model and emphasizes data consistency, integrity, and efficient querying.

**Core Concepts**
- Database: a logical container that holds multiple tables and related objects.
- Table (Relation): a structured dataset organized in rows and columns.
- Row (Tuple): a single record in a table.
- Column (Attribute): a field representing a specific property of the data.
- Schema: the definition of table structure, including columns, data types, and constraints.

## Keys and Constraints

**Primary Key**
- Uniquely identifies each row
- Cannot be NULL or duplicated

**Foreign Key**
- Establishes relationships between tables
- References a primary key in another table
- Can be NULL or duplicated, but must reference an existing value when not NULL

**Unique**
- Ensures all values in a column are distinct

**Not Null**
- Prevents NULL values

## SQL Operations (CRUD)

**Create**
INSERT INTO table VALUES (...)

**Read**
SELECT * FROM table

**Update**
UPDATE table SET column = value

**Delete**
DELETE FROM table WHERE condition

## Transactions and ACID

**Transaction**
A sequence of operations treated as a single unit.

Know about [[ACID|ACID]].

## Normalization

Normalization is the process of organizing data to reduce redundancy and avoid anomalies.

### 1NF (First Normal Form)

- **All fields are atomic**

Bad Example:

| user_id | phones  |
| ------- | ------- |
| 1       | 123,456 |

- **No repeating groups**

Bad Example:

| user_id | phone1 | phone2 |
|--------|--------|--------|
| 1      | 123    | 456    |

Good Example:

| user_id | phone |
|--------|------|
| 1      | 123  |
| 1      | 456  |

### 2NF (Second Normal Form)

- **Satisfies 1NF**
- **Non-key attributes fully depend on the primary key**

Bad Example:
Primary key: (order_id, product_id)

| order_id | product_id | product_name | quantity |
| -------- | ---------- | ------------ | -------- |
| 1        | 101        | Apple        | 2        |
| 1        | 102        | Banana       | 3        |

Good Example:

Table 1

| order_id | product_id | quantity |
| -------- | ---------- | -------- |
| 1        | 101        | 2        |
| 1        | 102        | 3        |

Table 2

| product_id | product_name |
| ---------- | ------------ |
| 101        | Apple        |
| 102        | Banana       |

### 3NF (Third Normal Form)

- **Satisfies 2NF**
- **No transitive dependency**
- **Non-key attributes depend only on the primary key**

Bad Example:

| order_id | user_id | user_name |
| -------- | ------- | --------- |
| 1        | 10      | Alice     |

Good Example:

Table 1

| order_id | user_id |
|----------|--------|
| 1        | 10     |

Table 2

| user_id | user_name |
|--------|----------|
| 10     | Alice    |

## JOIN

Used to combine data from multiple tables.

Types:
- INNER JOIN: return only rows that match in both tables (intersection)
- LEFT JOIN: return all rows from left table, unmatched right as NULL
- RIGHT JOIN: return all rows from right table, unmatched left as NULL
- FULL JOIN (FULL OUTER JOIN): return all rows from both tables, unmatched filled with NULL
- CROSS JOIN: return all possible combinations of rows (Cartesian product)

## Advantages

- Strong consistency
- Structured schema
- Powerful querying (SQL)
- Data integrity enforcement (ensuring data is accurate, consistent, and reliable)

## Trade-offs

- Less flexible schema
- Harder to scale horizontally
- JOIN operations can be expensive (distributing data and workload across multiple machines is complex due to joins, transactions, and consistency requirements)
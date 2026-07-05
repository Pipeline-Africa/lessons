# Week 2: Intermediate SQL & Database Design

## Learning Objectives

By the end of this week, you will be able to:

- Join two or more tables using INNER JOIN, LEFT JOIN, and multi-table JOINs
- Aggregate data using COUNT, SUM, AVG, MIN, MAX with GROUP BY and HAVING
- Filter and transform data with CASE WHEN
- Write subqueries and CTEs to break complex problems into steps
- Use window functions to rank, partition, and compute running totals
- Design a relational database with primary keys, foreign keys, and indexes

---

## Schedule

### Technical

| Day | Topic | File |
|-----|-------|------|
| 1 | Lab: Build the Retail Sales Database | [labs/lab2_retail_database.ipynb](labs/lab2_retail_database.ipynb) |
| 2 | JOINs | [lesson1_joins.ipynb](lesson1_joins.ipynb) |
| 3 | Aggregations, GROUP BY, HAVING, CASE WHEN | [lesson2_aggregations.ipynb](lesson2_aggregations.ipynb) |
| 4 | Subqueries, CTEs, Window Functions | [lesson3_advanced_sql.ipynb](lesson3_advanced_sql.ipynb) |
| 4–5 | Database Design: Keys, Constraints, Indexes | [lesson4_database_design.ipynb](lesson4_database_design.ipynb) |
| 5 | SQL Exercises | [exercises/sql_exercises.ipynb](exercises/sql_exercises.ipynb) |

> **Important:** Complete the Lab (Day 1) before any other lesson. All lessons and exercises use the retail database you build in the lab.

### Activities

| Activity | File |
|----------|------|
| Discussion + Reading Club + Project Review | [activities/week2_activities.ipynb](activities/week2_activities.ipynb) |

---

## The Retail Sales Database

This week's dataset models a retail business with five related tables:

```
customers ──< orders ──< order_items >── products
                 │
            salespeople
```

| Table | Rows | Description |
|-------|------|-------------|
| `customers` | 20 | Customer profiles — name, city, signup date |
| `products` | 15 | Product catalogue — name, category, price, cost |
| `salespeople` | 5 | Sales team — name, region, hire date |
| `orders` | 50 | Order header — which customer, which salesperson, date, status |
| `order_items` | 81 | Line items — which product, quantity, unit price |

---

## Prerequisites

- Week 1 complete — PostgreSQL running, pgAdmin working, SQL basics (SELECT/WHERE/ORDER BY)
- `de_course` database exists with the `de_student` user

---

## Files in This Week

```
week2/
├── README.md                                      ← You are here
├── lesson1_joins.ipynb                            ← INNER JOIN, LEFT JOIN, multi-table JOINs
├── lesson2_aggregations.ipynb                     ← COUNT/SUM/AVG, GROUP BY, HAVING, CASE WHEN
├── lesson3_advanced_sql.ipynb                     ← Subqueries, CTEs, window functions
├── lesson4_database_design.ipynb                  ← PKs, FKs, indexes, normalization
├── activities/
│   └── week2_activities.ipynb                    ← Discussion, Reading Club, Project Review
├── exercises/
│   └── sql_exercises.ipynb                       ← 33 exercises across all 4 lesson topics
├── labs/
│   └── lab2_retail_database.ipynb                ← Build and load the 5-table retail database
└── data/
    ├── customers.csv                              ← 20 customers
    ├── products.csv                               ← 15 products
    ├── salespeople.csv                            ← 5 salespeople
    ├── orders.csv                                 ← 50 orders
    └── order_items.csv                            ← 81 order line items
```

---

## Project

**Retail Sales Analytics Database**

The lab and lessons together build the foundation. The assignment uses everything from the week:

> Write SQL queries to produce a sales dashboard that shows:
> 1. Total revenue and order count by salesperson
> 2. Top 10 products by total units sold
> 3. Revenue by product category
> 4. Month-by-month revenue trend for 2023
> 5. Top 5 customers by total spend
> 6. Customer breakdown by city
> 7. Order status summary (completed vs pending vs cancelled)

Push your completed exercises and dashboard queries to GitHub before Week 3.

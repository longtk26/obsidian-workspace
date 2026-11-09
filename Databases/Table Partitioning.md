# Table Partitioning in PostgreSQL

> A practical guide to splitting large tables into manageable pieces for better performance and scalability.

---

## Table of Contents

1. [What is Table Partitioning?](#what-is-table-partitioning)
2. [Why Use Table Partitioning?](#why-use-table-partitioning)
3. [Types of Partitioning](#types-of-partitioning)
   - [Range Partitioning](#1-range-partitioning)
   - [List Partitioning](#2-list-partitioning)
   - [Hash Partitioning](#3-hash-partitioning)
   - [Composite Partitioning](#4-composite-partitioning)
4. [Step-by-Step Implementation](#step-by-step-implementation)
5. [When to Use Partitioning](#when-to-use-partitioning)
6. [Best Practices](#best-practices)
7. [Summary](#summary)

---

## What is Table Partitioning?

Table partitioning is a database design technique that **divides a single large table into smaller, physically separate sub-tables** called *partitions*. From the application's perspective, it still looks and behaves like one table — but under the hood, PostgreSQL routes data to and reads from the appropriate partition automatically.

Each partition holds a subset of the parent table's rows, determined by a partitioning strategy you define. Think of it like filing cabinets: instead of one giant cabinet for all documents, you have separate drawers organized by year, region, or category.

```
                  ┌──────────────────┐
                  │   orders (parent) │
                  └────────┬─────────┘
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
  orders_january    orders_february    orders_march
  (2023-01-*)        (2023-02-*)        (2023-03-*)
```

---

## Why Use Table Partitioning?

As tables grow into the hundreds of millions of rows, full table scans become expensive. Partitioning addresses this through several mechanisms:

| Benefit | How It Helps |
|---|---|
| **Query Performance** | PostgreSQL skips irrelevant partitions entirely (*partition pruning*) |
| **Faster Maintenance** | `DROP` or `TRUNCATE` a partition instead of deleting millions of rows |
| **Parallel Processing** | Queries can scan multiple partitions simultaneously |
| **Easier Archiving** | Detach old partitions and archive/delete them independently |
| **Optimized Storage** | Partitions can live on different tablespaces or storage tiers |

Without partitioning, a query like `WHERE order_date = '2023-01-15'` on a 500M-row table scans everything. With range partitioning by month, PostgreSQL only touches `orders_january` — a fraction of the data.

---

## Types of Partitioning

PostgreSQL supports four partitioning strategies. The right choice depends on your data shape and query patterns.

---

### 1. Range Partitioning

Splits rows based on **continuous value ranges** in a column. Best for time-series data, sequential IDs, or any data with a natural ordering.

**Use when:** You frequently query by date ranges, or regularly archive/purge old data.

```sql
CREATE TABLE sales (
    id         SERIAL,
    sale_date  DATE NOT NULL,
    amount     NUMERIC,
    PRIMARY KEY (id, sale_date)
) PARTITION BY RANGE (sale_date);

-- Create individual partitions
CREATE TABLE sales_2023_q1 PARTITION OF sales
    FOR VALUES FROM ('2023-01-01') TO ('2023-04-01');

CREATE TABLE sales_2023_q2 PARTITION OF sales
    FOR VALUES FROM ('2023-04-01') TO ('2023-07-01');
```

The upper bound is **exclusive** — `TO ('2023-04-01')` means up to but not including April 1st.

**Strengths:**
- Efficient range queries (`BETWEEN`, `>`, `<`)
- Easy to drop old data by detaching and dropping a partition

---

### 2. List Partitioning

Splits rows based on **discrete, known values** in a column. Best for categorical data like regions, status codes, or product types.

**Use when:** You filter frequently by a column with a small set of distinct values.

```sql
CREATE TABLE customers (
    id           SERIAL,
    country_code TEXT NOT NULL,
    name         TEXT,
    PRIMARY KEY (id, country_code)
) PARTITION BY LIST (country_code);

-- Each partition covers one or more explicit values
CREATE TABLE customers_us PARTITION OF customers
    FOR VALUES IN ('US');

CREATE TABLE customers_eu PARTITION OF customers
    FOR VALUES IN ('DE', 'FR', 'IT', 'ES', 'NL');

CREATE TABLE customers_apac PARTITION OF customers
    FOR VALUES IN ('JP', 'KR', 'SG', 'AU');
```

**Strengths:**
- Precise control over which rows go where
- Natural fit for multi-tenant or geo-sharded data

---

### 3. Hash Partitioning

Uses a **hash function on the partition key** to distribute rows evenly across a fixed number of partitions. Best when there's no natural grouping but you want uniform load distribution.

**Use when:** Your data has no obvious range or category, but you want to spread load evenly across partitions.

```sql
CREATE TABLE transactions (
    id      SERIAL,
    user_id INT NOT NULL,
    amount  NUMERIC,
    PRIMARY KEY (id, user_id)
) PARTITION BY HASH (user_id);

-- MODULUS = total number of partitions, REMAINDER = which one
CREATE TABLE transactions_p0 PARTITION OF transactions
    FOR VALUES WITH (MODULUS 4, REMAINDER 0);

CREATE TABLE transactions_p1 PARTITION OF transactions
    FOR VALUES WITH (MODULUS 4, REMAINDER 1);

CREATE TABLE transactions_p2 PARTITION OF transactions
    FOR VALUES WITH (MODULUS 4, REMAINDER 2);

CREATE TABLE transactions_p3 PARTITION OF transactions
    FOR VALUES WITH (MODULUS 4, REMAINDER 3);
```

**Strengths:**
- Guarantees even data distribution
- No hotspots from skewed key values

**Trade-off:** Range queries don't benefit — partition pruning can't apply since hashing destroys ordering.

---

### 4. Composite Partitioning

**Combines two strategies** by sub-partitioning a partition with a second method. Useful when your data has two meaningful dimensions to organize by.

**Use when:** Data has both a time dimension and a categorical dimension (e.g., orders by region and by date).

```sql
-- Parent partitioned by region (List)
CREATE TABLE orders (
    id         SERIAL,
    region     TEXT NOT NULL,
    order_date DATE NOT NULL,
    amount     NUMERIC,
    PRIMARY KEY (id, region, order_date)
) PARTITION BY LIST (region);

-- Each region partition is further split by date (Range)
CREATE TABLE orders_us PARTITION OF orders
    FOR VALUES IN ('US')
    PARTITION BY RANGE (order_date);

CREATE TABLE orders_us_2023_q1 PARTITION OF orders_us
    FOR VALUES FROM ('2023-01-01') TO ('2023-04-01');

CREATE TABLE orders_us_2023_q2 PARTITION OF orders_us
    FOR VALUES FROM ('2023-04-01') TO ('2023-07-01');
```

A query filtered on both `region = 'US'` and a date range will prune down to a single leaf partition — maximally efficient.

---

## Step-by-Step Implementation

Here's a complete end-to-end example using range partitioning on an `orders` table.

**Step 1 — Create the partitioned parent table**

```sql
CREATE TABLE orders (
    order_id    SERIAL,
    order_date  DATE NOT NULL,
    customer_id INT,
    amount      NUMERIC,
    PRIMARY KEY (order_id, order_date)
) PARTITION BY RANGE (order_date);
```

Note: the partition key (`order_date`) **must be part of the primary key**.

**Step 2 — Create the partitions**

```sql
CREATE TABLE orders_january PARTITION OF orders
    FOR VALUES FROM ('2023-01-01') TO ('2023-02-01');

CREATE TABLE orders_february PARTITION OF orders
    FOR VALUES FROM ('2023-02-01') TO ('2023-03-01');

CREATE TABLE orders_march PARTITION OF orders
    FOR VALUES FROM ('2023-03-01') TO ('2023-04-01');
```

**Step 3 — Insert data (routes automatically)**

```sql
INSERT INTO orders (order_date, customer_id, amount) VALUES
    ('2023-01-15', 1, 100.00),
    ('2023-02-10', 2, 200.00),
    ('2023-03-05', 3, 150.00);
```

PostgreSQL automatically routes each row to the correct partition.

**Step 4 — Verify partition pruning with EXPLAIN**

```sql
EXPLAIN SELECT * FROM orders WHERE order_date = '2023-01-15';
```

You should see only `orders_january` in the query plan — the other partitions are pruned.

**Step 5 — Add indexes on partitions**

```sql
-- Index on the parent propagates to all partitions
CREATE INDEX ON orders (customer_id);
```

**Step 6 — Maintain partitions over time**

```sql
-- Add a new month partition
CREATE TABLE orders_april PARTITION OF orders
    FOR VALUES FROM ('2023-04-01') TO ('2023-05-01');

-- Archive old data: detach, then optionally drop or move
ALTER TABLE orders DETACH PARTITION orders_january;
DROP TABLE orders_january; -- or archive it elsewhere
```

---

## When to Use Partitioning

Partitioning adds complexity — only reach for it when the trade-off is worth it.

**Good candidates:**
- Tables exceeding ~10–50 million rows with continued growth
- Time-series data where old records are regularly archived or deleted
- Queries that almost always filter on the partition key
- Tables where `VACUUM` and index maintenance are becoming slow

**Not a good fit:**
- Small or medium tables where sequential scans are fast enough
- Tables with highly random access patterns that don't filter on a single key
- Situations where the partition key is rarely used in `WHERE` clauses (pruning won't help)
- When the overhead of managing partitions outweighs the gains

---

## Best Practices

**Choose partition keys that match your query patterns.** If 90% of queries filter by `order_date`, that's your key. A key you never filter on gives you no pruning benefit.

**Pre-create future partitions.** Rows inserted into a partitioned table with no matching partition cause an error. Automate partition creation with a cron job or a `pg_partman` extension.

**Keep partitions balanced.** Uneven partitions (one partition with 10x the rows of others) defeats the purpose. Monitor partition sizes regularly.

**Use `pg_partman` for automation.** Managing dozens of time-based partitions by hand is error-prone. `pg_partman` handles creation, retention, and cleanup automatically.

```sql
-- pg_partman example: auto-manage monthly partitions
SELECT partman.create_parent(
    p_parent_table => 'public.orders',
    p_control      => 'order_date',
    p_type         => 'range',
    p_interval     => 'monthly'
);
```

**Test with `EXPLAIN (ANALYZE, BUFFERS)`.** Always verify partition pruning is actually happening. A missing constraint or implicit cast can silently disable it.

**Avoid cross-partition joins on hot paths.** Joins between two large partitioned tables can be expensive if pruning doesn't apply to both sides.

---

## Summary

Table partitioning is a powerful scaling technique for PostgreSQL when your tables grow beyond the point where indexes alone keep queries fast.

| Strategy | Split By | Best For |
|---|---|---|
| **Range** | Continuous ranges | Dates, timestamps, sequential IDs |
| **List** | Discrete values | Regions, categories, status codes |
| **Hash** | Hash of key | Even distribution, no natural grouping |
| **Composite** | Two strategies combined | Multi-dimensional data |

The core payoff is **partition pruning** — PostgreSQL skips partitions that can't possibly contain the rows you need. Combine that with easier bulk deletes and parallel scan potential, and partitioning can turn a painfully slow query into a fast one without changing application code.

Start with the simplest strategy that matches your access patterns, verify pruning with `EXPLAIN`, and automate partition lifecycle management from day one.

---

*Source: [How to Use Table Partitioning to Scale PostgreSQL — GeeksforGeeks](https://www.geeksforgeeks.org/postgresql/how-to-use-table-partitioning-to-scale-postgresql/)*

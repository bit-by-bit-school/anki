---
name: SQL
---

# What are the four main SQL sublanguages and what does each cover?

SQL is divided into four sublanguages by purpose:

| Sublanguage | Stands for                   | Commands                                          |
| ----------- | ---------------------------- | ------------------------------------------------- |
| **DDL**     | Data Definition Language     | `CREATE`, `ALTER`, `DROP`, `RENAME`               |
| **DML**     | Data Manipulation Language   | `INSERT`, `UPDATE`, `DELETE`, `MERGE`, `TRUNCATE` |
| **DQL**     | Data Query Language          | `SELECT`                                          |
| **DCL**     | Data Control Language        | `GRANT`, `REVOKE`                                 |
| **TCL**     | Transaction Control Language | `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT`        |

Most interview questions focus on DML and DDL. Understanding the distinction helps you reason about what each statement does to the schema vs. the data.

**Source:** <https://www.postgresql.org/docs/current/sql-commands.html>

<AnkiTags DDL DML DCL TCL overview junior/>

# What is the syntax of the CREATE TABLE statement?

```sql
CREATE TABLE table_name (
  column1 datatype constraint,
  column2 datatype constraint,
  column3 datatype constraint,
  ....
);
```

**Source:** <https://www.w3schools.com/sql/sql_create_table.asp>

# What is the syntax of the DROP TABLE statement?

```sql
DROP TABLE table_name;
```

To prevent an error from occur (if the table does not exists), it is a good practice to add the IF EXISTS clause:

```sql
DROP TABLE IF EXISTS table_name;
```

Source: <https://www.w3schools.com/sql/sql_drop_table.asp>

# What is the logical order of execution of a SQL SELECT statement?

SQL clauses are _written_ in one order but _executed_ in a different order:

```
1.  FROM          — identify source tables
2.  JOIN          — combine rows from joined tables
3.  WHERE         — filter individual rows
4.  GROUP BY      — collapse rows into groups
5.  HAVING        — filter groups
6.  SELECT        — compute output expressions / aliases
7.  DISTINCT      — remove duplicate rows
8.  ORDER BY      — sort the result set
9.  LIMIT/OFFSET  — restrict the number of rows returned
```

Key consequences:

- Column **aliases** defined in `SELECT` are **not available** in `WHERE` or `GROUP BY` (they haven't been computed yet).
- `HAVING` filters groups _after_ aggregation; `WHERE` filters rows _before_ aggregation.
- Window functions are evaluated after `WHERE`, `GROUP BY`, and `HAVING`, but before the final `ORDER BY`.

**Source:** <https://sqlbolt.com/lesson/select_queries_order_of_execution>

<AnkiTags execution-order SELECT fundamentals intermediate/>

# What is the difference between WHERE and HAVING?

`WHERE` filters **individual rows** before grouping or aggregation — it cannot reference aggregate functions.

`HAVING` filters **groups** after `GROUP BY` and aggregation — it can reference aggregate functions.

```sql
-- WHERE: filter rows before grouping
SELECT department, COUNT(*) AS headcount
FROM employees
WHERE status = 'active'          -- excludes inactive rows first
GROUP BY department
HAVING COUNT(*) > 10;            -- then filters groups with <= 10
```

A common mistake is using `WHERE COUNT(*) > 10` which is a syntax error — aggregate functions are not allowed in `WHERE`.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUP>

<AnkiTags WHERE HAVING GROUP-BY filtering junior intermediate/>

# What is the difference between DELETE and TRUNCATE?

`DELETE` removes rows one at a time, fires row-level triggers, respects `WHERE`, is fully logged, and can be rolled back inside a transaction.

`TRUNCATE` removes **all rows** from a table by deallocating data pages. It is much faster on large tables, does not fire row-level triggers (fires statement-level `TRUNCATE` triggers), cannot use `WHERE`, and in PostgreSQL is also transactional (can be rolled back).

```sql
DELETE FROM logs WHERE created_at < '2023-01-01';  -- selective, logged
DELETE FROM logs;                                   -- all rows, slow on large tables

TRUNCATE TABLE logs;                                -- fast bulk removal, no WHERE
TRUNCATE TABLE orders, order_items;                 -- multiple tables
```

In MySQL, `TRUNCATE` implicitly commits the current transaction and resets `AUTO_INCREMENT`.

**Source:** <https://www.postgresql.org/docs/current/sql-truncate.html>
<https://www.postgresql.org/docs/current/sql-delete.html>

<AnkiTags DELETE TRUNCATE DML junior intermediate/>

# What are SQL constraints? List the six main ones

Constraints enforce data integrity rules at the schema level. They are checked on every `INSERT` or `UPDATE`.

| Constraint    | Purpose                                                   |
| ------------- | --------------------------------------------------------- |
| `PRIMARY KEY` | Uniquely identifies each row; implies `NOT NULL + UNIQUE` |
| `FOREIGN KEY` | Enforces referential integrity to another table's key     |
| `UNIQUE`      | No two rows can have the same value(s) for the column(s)  |
| `NOT NULL`    | The column cannot contain `NULL`                          |
| `CHECK`       | A boolean expression that every row must satisfy          |
| `DEFAULT`     | Provides a value when none is supplied on `INSERT`        |

```sql
CREATE TABLE orders (
    order_id   SERIAL        PRIMARY KEY,
    customer_id INT          NOT NULL REFERENCES customers(id),
    status     VARCHAR(20)   NOT NULL DEFAULT 'pending'
                             CHECK (status IN ('pending','shipped','cancelled')),
    amount     NUMERIC(10,2) CHECK (amount > 0)
);
```

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html>

<AnkiTags DDL constraints PRIMARY-KEY FOREIGN-KEY junior/>

# What does a PRIMARY KEY do, and can a table have more than one?

A `PRIMARY KEY` uniquely identifies every row in a table. It implicitly enforces `NOT NULL` and `UNIQUE` on the column(s). A table can have **only one** primary key, but the key can span **multiple columns** (a composite primary key).

```sql
-- Single-column PK
CREATE TABLE users (
    id    SERIAL PRIMARY KEY,
    email TEXT   NOT NULL UNIQUE
);

-- Composite PK (junction table)
CREATE TABLE enrollments (
    student_id INT  REFERENCES students(id),
    course_id  INT  REFERENCES courses(id),
    enrolled_at DATE NOT NULL,
    PRIMARY KEY (student_id, course_id)
);
```

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-PRIMARY-KEYS>

<AnkiTags DDL PRIMARY-KEY constraints junior/>

# What are ON DELETE and ON UPDATE referential actions on a FOREIGN KEY?

When a referenced row is deleted or updated, the database can respond in several ways:

| Action        | Behaviour                                                            |
| ------------- | -------------------------------------------------------------------- |
| `RESTRICT`    | Raise an error immediately (default in most engines)                 |
| `NO ACTION`   | Like `RESTRICT` but checked at end of statement (PostgreSQL default) |
| `CASCADE`     | Automatically delete/update child rows                               |
| `SET NULL`    | Set the FK column(s) in child rows to `NULL`                         |
| `SET DEFAULT` | Set the FK column(s) to their default value                          |

```sql
CREATE TABLE order_items (
    id         SERIAL PRIMARY KEY,
    order_id   INT REFERENCES orders(id) ON DELETE CASCADE,
    product_id INT REFERENCES products(id) ON DELETE RESTRICT,
    qty        INT NOT NULL
);
```

Use `CASCADE` with caution — a single `DELETE` can silently remove thousands of child rows.

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-FK>

<AnkiTags DDL FOREIGN-KEY referential-integrity intermediate/>

# How do you add or drop a column from an existing table?

Use `ALTER TABLE` with `ADD COLUMN` or `DROP COLUMN`.

```sql
-- Add a column (new column is NULL for all existing rows unless DEFAULT supplied)
ALTER TABLE employees
    ADD COLUMN hired_at DATE NOT NULL DEFAULT CURRENT_DATE;

-- Add multiple columns
ALTER TABLE products
    ADD COLUMN weight_kg NUMERIC(6,2),
    ADD COLUMN is_active BOOLEAN NOT NULL DEFAULT TRUE;

-- Drop a column
ALTER TABLE employees
    DROP COLUMN middle_name;

-- Drop a column and any objects that depend on it
ALTER TABLE employees
    DROP COLUMN dept_code CASCADE;

-- Rename a column
ALTER TABLE employees
    RENAME COLUMN hire_date TO hired_at;
```

**Source:** <https://www.postgresql.org/docs/current/sql-altertable.html>

<AnkiTags DDL ALTER-TABLE junior/>

# What is the difference between CHAR, VARCHAR, and TEXT data types?

| Type         | Storage                  | Padding          | Use case                                       |
| ------------ | ------------------------ | ---------------- | ---------------------------------------------- |
| `CHAR(n)`    | Fixed — always n bytes   | Pads with spaces | Codes of exact length (e.g. ISO country codes) |
| `VARCHAR(n)` | Variable — up to n chars | No padding       | General strings with a known max length        |
| `TEXT`       | Unlimited variable       | No padding       | Long strings, no artificial limit              |

In PostgreSQL, all three have equivalent performance — `TEXT` is the preferred general-purpose type. In MySQL, `TEXT` is stored off-page beyond 65 535 bytes and cannot have a `DEFAULT` other than `NULL`.

```sql
CREATE TABLE countries (
    code    CHAR(2)       NOT NULL,   -- always exactly 2 chars
    name    VARCHAR(100)  NOT NULL,
    notes   TEXT
);
```

**Source:** <https://www.postgresql.org/docs/current/datatype-character.html>
<https://dev.mysql.com/doc/refman/8.0/en/string-type-syntax.html>

<AnkiTags DDL datatypes CHAR VARCHAR TEXT junior/>

# What SQL numeric types should you know, and when do you use NUMERIC vs FLOAT?

| Type                            | Description                               | Use for                      |
| ------------------------------- | ----------------------------------------- | ---------------------------- |
| `SMALLINT`                      | 2-byte integer (-32768 to 32767)          | Small counts                 |
| `INTEGER` / `INT`               | 4-byte integer                            | General IDs and counts       |
| `BIGINT`                        | 8-byte integer                            | Large IDs, row counts        |
| `NUMERIC(p,s)` / `DECIMAL(p,s)` | Exact decimal (p digits, s after decimal) | Money, taxes, anything exact |
| `REAL` / `FLOAT4`               | 4-byte IEEE 754 float                     | Scientific, approximate      |
| `DOUBLE PRECISION` / `FLOAT8`   | 8-byte IEEE 754 float                     | Scientific, approximate      |

Use `NUMERIC` for **monetary values** — floating-point types accumulate rounding errors:

```sql
SELECT 0.1::FLOAT + 0.2::FLOAT = 0.3::FLOAT;  -- false (rounding error)
SELECT 0.1::NUMERIC + 0.2::NUMERIC = 0.3::NUMERIC;  -- true (exact)
```

**Source:** <https://www.postgresql.org/docs/current/datatype-numeric.html>

<AnkiTags DDL datatypes NUMERIC FLOAT junior intermediate/>

# What is the difference between INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL OUTER JOIN?

All JOINs combine rows from two tables based on a condition. They differ in how they handle **rows with no match**:

| Join type            | Rows kept                                                            |
| -------------------- | -------------------------------------------------------------------- |
| `INNER JOIN`         | Only rows with a match in **both** tables                            |
| `LEFT [OUTER] JOIN`  | All rows from the **left** table; `NULL` for unmatched right columns |
| `RIGHT [OUTER] JOIN` | All rows from the **right** table; `NULL` for unmatched left columns |
| `FULL [OUTER] JOIN`  | All rows from **both** tables; `NULL` where there is no match        |

```sql
SELECT e.name, d.name AS dept
FROM   employees e
LEFT   JOIN departments d ON e.dept_id = d.id;
-- employees with no department appear with dept = NULL
```

`RIGHT JOIN` is rarely used — you can always rewrite it as a `LEFT JOIN` by swapping table order.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN INNER LEFT RIGHT FULL-OUTER junior/>

# What is a CROSS JOIN and when would you use it?

A `CROSS JOIN` returns the **Cartesian product** of two tables — every row from the left table paired with every row from the right table. It has no `ON` condition.

If table A has m rows and table B has n rows, the result has m × n rows.

```sql
-- Generate all combinations of sizes and colours
SELECT s.size, c.colour
FROM   sizes s
CROSS  JOIN colours c;
-- 3 sizes × 4 colours = 12 rows

-- Old implicit syntax (equivalent, avoid in new code):
SELECT s.size, c.colour
FROM   sizes s, colours c;
```

Use cases: generating test data, creating a time-series grid, combinatorial lookups.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN CROSS-JOIN intermediate/>

# What is a SELF JOIN and when do you use it?

A self join joins a table **to itself** — the same table appears twice under different aliases. Used when rows in a table reference other rows in the same table (hierarchical or recursive data).

```sql
-- Employees with their manager's name
SELECT e.name   AS employee,
       m.name   AS manager
FROM   employees e
LEFT   JOIN employees m ON e.manager_id = m.id;

-- Find pairs of employees in the same department
SELECT a.name, b.name, a.department
FROM   employees a
JOIN   employees b ON a.department = b.department
                  AND a.id < b.id;   -- avoid (a,b) and (b,a) duplicates
```

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN SELF-JOIN intermediate/>

# What is the difference between IN and EXISTS in a subquery, and which is faster?

`IN` evaluates a subquery once, collects the result set, and checks membership. It performs poorly when the subquery returns a large result.

`EXISTS` checks whether a subquery returns **at least one row** — it short-circuits as soon as it finds a match. It is generally faster for large subquery results.

```sql
-- IN: retrieves all dept IDs, then checks membership
SELECT name FROM employees
WHERE dept_id IN (SELECT id FROM departments WHERE location = 'NYC');

-- EXISTS: stops as soon as first match is found
SELECT name FROM employees e
WHERE EXISTS (
    SELECT 1 FROM departments d
    WHERE d.id = e.dept_id AND d.location = 'NYC'
);
```

`NOT EXISTS` is usually preferable to `NOT IN` — `NOT IN` returns no rows if **any** value in the subquery is `NULL`, which is a common bug.

**Source:** <https://www.postgresql.org/docs/current/functions-subquery.html>

<AnkiTags subqueries IN EXISTS performance intermediate/>

# What is a correlated subquery?

A correlated subquery references a column from the **outer query**. It is re-evaluated once for every row of the outer query, which can be expensive.

```sql
-- For each employee, find those earning above their department's average
SELECT name, salary, dept_id
FROM   employees e1
WHERE  salary > (
    SELECT AVG(salary)
    FROM   employees e2
    WHERE  e2.dept_id = e1.dept_id   -- references outer query's e1
);
```

The subquery above runs once per row of `employees`. For large tables, consider rewriting with a window function or a CTE with a `JOIN` instead.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-LATERAL>

<AnkiTags subqueries correlated intermediate advanced/>

# What is a CTE (Common Table Expression) and what is its syntax?

A CTE defines a **named temporary result set** scoped to a single SQL statement. It improves readability and can be referenced multiple times in the same query.

```sql
WITH
-- First CTE
active_customers AS (
    SELECT id, name, region
    FROM   customers
    WHERE  status = 'active'
),
-- Second CTE (can reference previous CTEs)
regional_totals AS (
    SELECT ac.region, COUNT(*) AS cnt
    FROM   active_customers ac
    GROUP  BY ac.region
)
SELECT region, cnt
FROM   regional_totals
WHERE  cnt > 100
ORDER  BY cnt DESC;
```

In PostgreSQL, CTEs are **optimisation fences** by default (prior to PG 12) — each CTE is materialised once. In PG 12+ the optimiser may inline them; add `MATERIALIZED` or `NOT MATERIALIZED` to control this explicitly.

**Source:** <https://www.postgresql.org/docs/current/queries-with.html>

<AnkiTags CTE WITH intermediate/>

# What is a recursive CTE and how does it work?

A recursive CTE references itself to process **hierarchical or graph-structured data** (e.g., org charts, category trees, bill of materials).

Structure: `WITH RECURSIVE name AS (base_case UNION ALL recursive_case)`

```sql
-- Walk an employee hierarchy top-down
WITH RECURSIVE org_chart AS (
    -- Base case: the CEO (no manager)
    SELECT id, name, manager_id, 1 AS depth
    FROM   employees
    WHERE  manager_id IS NULL

    UNION ALL

    -- Recursive step: join each employee to their manager row
    SELECT e.id, e.name, e.manager_id, oc.depth + 1
    FROM   employees e
    JOIN   org_chart oc ON e.manager_id = oc.id
)
SELECT * FROM org_chart ORDER BY depth, name;
```

The engine alternates between the base case and the recursive step until no new rows are produced. Always include a depth/cycle guard to prevent infinite loops on cyclic data.

**Source:** <https://www.postgresql.org/docs/current/queries-with.html#QUERIES-WITH-RECURSIVE>

<AnkiTags CTE recursive WITH advanced/>

# What is a window function, and how does it differ from GROUP BY?

A **window function** performs a calculation across a set of rows related to the current row (the "window"), **without collapsing rows**. `GROUP BY` collapses many rows into one per group; window functions keep every row and add an extra computed column alongside it.

```sql
SELECT
    name,
    department,
    salary,
    AVG(salary) OVER (PARTITION BY department) AS dept_avg,
    salary - AVG(salary) OVER (PARTITION BY department) AS diff_from_avg
FROM employees;
-- Every row is preserved; dept_avg is the average for that row's department
```

Syntax: `function() OVER ([PARTITION BY ...] [ORDER BY ...] [frame_clause])`

**Source:** <https://www.postgresql.org/docs/current/tutorial-window.html>

<AnkiTags window-functions OVER PARTITION-BY intermediate/>

# What is the difference between ROW_NUMBER, RANK, and DENSE_RANK?

All three assign an integer to rows within a window ordered by `ORDER BY`. They differ in how they handle **ties**:

```sql
SELECT name, score,
    ROW_NUMBER()  OVER (ORDER BY score DESC) AS row_num,
    RANK()        OVER (ORDER BY score DESC) AS rnk,
    DENSE_RANK()  OVER (ORDER BY score DESC) AS dense_rnk
FROM scores;
```

| name  | score | ROW_NUMBER | RANK | DENSE_RANK |
| ----- | ----- | ---------- | ---- | ---------- |
| Alice | 95    | 1          | 1    | 1          |
| Bob   | 90    | 2          | 2    | 2          |
| Carol | 90    | 3          | 2    | 2          |
| Dave  | 85    | 4          | 4    | 3          |

- `ROW_NUMBER`: always unique, arbitrary tie-breaking
- `RANK`: ties share a rank, then skips numbers (gap after ties)
- `DENSE_RANK`: ties share a rank, **no gaps**

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions ROW-NUMBER RANK DENSE-RANK intermediate/>

# What do LAG and LEAD window functions do?

`LAG(expr, offset, default)` returns the value of `expr` from a row **offset rows before** the current row in the window.

`LEAD(expr, offset, default)` returns the value from a row **offset rows after** the current row.

Both are used to compare a row with its neighbours — e.g. month-over-month change.

```sql
SELECT
    month,
    revenue,
    LAG(revenue, 1, 0)  OVER (ORDER BY month) AS prev_month_rev,
    LEAD(revenue, 1, 0) OVER (ORDER BY month) AS next_month_rev,
    revenue - LAG(revenue, 1, 0) OVER (ORDER BY month) AS mom_change
FROM monthly_sales;
```

`offset` defaults to 1; `default` is returned when there is no preceding/following row.

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions LAG LEAD intermediate advanced/>

# What is PARTITION BY in a window function?

`PARTITION BY` divides the result set into independent **partitions**; the window function is applied separately within each partition — similar to how `GROUP BY` divides rows but without collapsing them.

```sql
-- Running total of sales, reset for each region
SELECT
    region,
    sale_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY region
        ORDER BY sale_date
    ) AS running_total
FROM sales;
```

Without `PARTITION BY`, the window spans **all rows** in the result set.

Multiple window functions in the same query can use different `PARTITION BY` columns independently.

**Source:** <https://www.postgresql.org/docs/current/tutorial-window.html>

<AnkiTags window-functions PARTITION-BY intermediate/>

# What is the frame clause in a window function (ROWS vs RANGE BETWEEN)?

The **frame clause** defines which rows relative to the current row are included in the window. It comes after `ORDER BY`.

```sql
-- ROWS: physical row offsets
SUM(amount) OVER (
    ORDER BY sale_date
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW   -- last 3 rows
)

-- RANGE: logical value range
SUM(amount) OVER (
    ORDER BY sale_date
    RANGE BETWEEN INTERVAL '6 days' PRECEDING AND CURRENT ROW
)
```

| Frame keyword         | Meaning                             |
| --------------------- | ----------------------------------- |
| `UNBOUNDED PRECEDING` | From the first row of the partition |
| `n PRECEDING`         | n rows/values before current row    |
| `CURRENT ROW`         | The current row                     |
| `n FOLLOWING`         | n rows/values after current row     |
| `UNBOUNDED FOLLOWING` | To the last row of the partition    |

Default (when `ORDER BY` is present): `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`.

**Source:** <https://www.postgresql.org/docs/current/sql-expressions.html#SYNTAX-WINDOW-FUNCTIONS>

<AnkiTags window-functions frame ROWS RANGE advanced/>

# What is NTILE and when would you use it?

`NTILE(n)` divides ordered rows within a partition into `n` **approximately equal buckets** and assigns each row a bucket number (1 through n).

```sql
-- Divide customers into 4 spending quartiles
SELECT
    customer_id,
    total_spend,
    NTILE(4) OVER (ORDER BY total_spend DESC) AS quartile
FROM customer_totals;
-- quartile 1 = top 25% spenders
```

Use cases: percentile bucketing, A/B test group assignment, load distribution.

If the number of rows is not evenly divisible by n, earlier buckets get one extra row.

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions NTILE intermediate/>

# What is the difference between FIRST_VALUE and LAST_VALUE in window functions?

`FIRST_VALUE(expr)` returns `expr` evaluated at the **first row** of the window frame.
`LAST_VALUE(expr)` returns `expr` evaluated at the **last row** of the window frame.

`LAST_VALUE` is commonly misunderstood — with the default frame (`UNBOUNDED PRECEDING TO CURRENT ROW`), it returns the **current row's own value**, not the partition's last row. You must explicitly extend the frame:

```sql
SELECT
    name, salary, department,
    FIRST_VALUE(salary) OVER w AS lowest_in_dept,
    LAST_VALUE(salary)  OVER w AS highest_in_dept
FROM employees
WINDOW w AS (
    PARTITION BY department
    ORDER BY salary
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
);
```

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions FIRST-VALUE LAST-VALUE advanced/>

# How does NULL behave in SQL comparisons?

`NULL` represents an unknown value. Any comparison with `NULL` using `=`, `!=`, `<`, `>` returns **`NULL`** (unknown), not `TRUE` or `FALSE`. This means rows with `NULL` are silently excluded from `WHERE` clauses.

```sql
SELECT 1 = NULL;    -- NULL (not FALSE)
SELECT NULL = NULL; -- NULL (not TRUE!)
SELECT NULL IS NULL; -- TRUE  ← correct way to test
SELECT NULL IS NOT NULL; -- FALSE

-- This does NOT return rows where dept_id is NULL:
SELECT * FROM employees WHERE dept_id = NULL;  -- wrong

-- Correct:
SELECT * FROM employees WHERE dept_id IS NULL;
```

`NULL` in `NOT IN`: if the subquery contains any `NULL`, `NOT IN` returns no rows — use `NOT EXISTS` instead.

**Source:** <https://www.postgresql.org/docs/current/functions-comparison.html>

<AnkiTags NULL comparisons pitfalls junior intermediate/>

# What does COALESCE do?

`COALESCE(expr1, expr2, ..., exprN)` returns the **first non-NULL argument**. It short-circuits — once a non-NULL value is found, subsequent arguments are not evaluated.

```sql
-- Replace NULL with a default
SELECT name, COALESCE(phone, 'N/A') AS phone
FROM contacts;

-- Chain multiple fallbacks
SELECT COALESCE(nickname, first_name, 'Unknown') AS display_name
FROM users;

-- Safe arithmetic with NULLs
SELECT COALESCE(price, 0) * COALESCE(quantity, 0) AS total
FROM order_items;
```

Use `COALESCE` instead of a `CASE` expression when you simply want the first non-NULL value — it is more concise and standard SQL.

**Source:** <https://www.postgresql.org/docs/current/functions-conditional.html#FUNCTIONS-COALESCE-NVL-IFNULL>

<AnkiTags NULL COALESCE functions junior intermediate/>

# What does NULLIF do?

`NULLIF(expr1, expr2)` returns `NULL` if `expr1 = expr2`, otherwise returns `expr1`. It is the inverse of `COALESCE` for a specific value.

```sql
-- Avoid division by zero
SELECT total_sales / NULLIF(num_transactions, 0) AS avg_sale
FROM summaries;
-- Returns NULL instead of raising division-by-zero error

-- Convert sentinel value to NULL
SELECT NULLIF(status, 'N/A') AS status
FROM records;
-- 'N/A' becomes NULL; other values are unchanged
```

**Source:** <https://www.postgresql.org/docs/current/functions-conditional.html#FUNCTIONS-NULLIF>

<AnkiTags NULL NULLIF functions intermediate/>

# How does NULL behave in aggregate functions?

All aggregate functions **ignore NULL values**, except `COUNT(*)`.

```sql
-- Table: scores = {10, 20, NULL, 30}
SELECT COUNT(*)     FROM t;  -- 4  (counts all rows including NULL)
SELECT COUNT(score) FROM t;  -- 3  (ignores NULL)
SELECT SUM(score)   FROM t;  -- 60 (ignores NULL)
SELECT AVG(score)   FROM t;  -- 20 (60 / 3, not 60 / 4!)
SELECT MIN(score)   FROM t;  -- 10
SELECT MAX(score)   FROM t;  -- 30
```

`AVG` ignoring NULLs is a common trap — `AVG(score)` is not the same as `SUM(score) / COUNT(*)` when NULLs are present. Use `COALESCE` if you want NULLs treated as zero.

**Source:** <https://www.postgresql.org/docs/current/functions-aggregate.html>

<AnkiTags NULL aggregate COUNT AVG intermediate/>

# What is the difference between COUNT(\*), COUNT(column), and COUNT(DISTINCT column)?

```sql
-- Sample table: ratings(id, user_id, score)
-- Rows: (1,1,5), (2,2,5), (3,3,NULL), (4,1,4)

SELECT
    COUNT(*)              AS total_rows,     -- 4  (every row)
    COUNT(score)          AS non_null_scores, -- 3  (ignores NULL)
    COUNT(DISTINCT score) AS unique_scores,   -- 2  (ignores NULL, deduplicates: 5,4)
    COUNT(DISTINCT user_id) AS unique_users   -- 3  (1,2,3)
FROM ratings;
```

- `COUNT(*)` — counts all rows in the group regardless of NULLs.
- `COUNT(col)` — counts rows where `col` is not NULL.
- `COUNT(DISTINCT col)` — counts distinct non-NULL values of `col`.

**Source:** <https://www.postgresql.org/docs/current/functions-aggregate.html>

<AnkiTags aggregate COUNT DISTINCT NULL junior intermediate/>

# What are GROUP BY and HAVING? Provide a combined example

`GROUP BY` collapses rows with the same value(s) in the specified column(s) into a single summary row. Every column in the `SELECT` list must either appear in `GROUP BY` or be wrapped in an aggregate function.

`HAVING` then filters those groups, like a `WHERE` for aggregated results.

```sql
SELECT
    d.name           AS department,
    COUNT(e.id)      AS headcount,
    ROUND(AVG(e.salary), 2) AS avg_salary,
    MAX(e.salary)    AS top_salary
FROM   employees e
JOIN   departments d ON e.dept_id = d.id
WHERE  e.status = 'active'
GROUP  BY d.name
HAVING COUNT(e.id) >= 5           -- only departments with 5+ employees
   AND AVG(e.salary) > 60000
ORDER  BY avg_salary DESC;
```

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUP>

<AnkiTags GROUP-BY HAVING aggregate junior intermediate/>

# What is DISTINCT and what are its performance implications?

`SELECT DISTINCT` removes duplicate rows from the result set. It is equivalent to `GROUP BY` on all selected columns without aggregation.

```sql
SELECT DISTINCT department FROM employees;    -- unique department names
SELECT DISTINCT ON (department) *             -- PostgreSQL only: first row per dept
FROM employees ORDER BY department, salary DESC;
```

**Performance:** `DISTINCT` requires sorting or hashing the entire result set to find duplicates — it is an O(n log n) operation. Common mistakes:

- Using `DISTINCT` to mask a broken `JOIN` that creates duplicates — fix the join instead.
- Using `DISTINCT` unnecessarily when the data is already unique.
- `SELECT DISTINCT` on a large result set is expensive; consider whether the duplicates should be prevented at query-design level.

**Source:** <https://www.postgresql.org/docs/current/queries-select-lists.html#QUERIES-DISTINCT>

<AnkiTags SELECT DISTINCT performance intermediate/>

# What is UNION vs UNION ALL?

Both combine the result sets of two `SELECT` statements that have the **same number of columns with compatible types**.

`UNION` removes duplicate rows (performs a sort/hash deduplication step) — slower.
`UNION ALL` keeps all rows including duplicates — faster, no extra dedup pass.

```sql
-- All active users and all admins (may overlap)
SELECT id, name FROM users   WHERE status = 'active'
UNION
SELECT id, name FROM admins;          -- deduplicates

-- All rows from two audit tables
SELECT * FROM audit_2023
UNION ALL
SELECT * FROM audit_2024;             -- keeps every row, faster
```

Prefer `UNION ALL` whenever duplicates are impossible (different tables) or acceptable.

**Source:** <https://www.postgresql.org/docs/current/queries-union.html>

<AnkiTags set-operations UNION UNION-ALL junior intermediate/>

# What are INTERSECT and EXCEPT?

`INTERSECT` returns only rows that appear in **both** result sets.
`EXCEPT` returns rows from the **first** result set that do **not** appear in the second.

```sql
-- Customers who placed orders AND wrote reviews
SELECT customer_id FROM orders
INTERSECT
SELECT customer_id FROM reviews;

-- Customers who placed orders but never wrote a review
SELECT customer_id FROM orders
EXCEPT
SELECT customer_id FROM reviews;
```

Both remove duplicates by default; `INTERSECT ALL` and `EXCEPT ALL` preserve them (less commonly used). MySQL does not support `INTERSECT`/`EXCEPT` natively before version 8.0.31.

**Source:** <https://www.postgresql.org/docs/current/queries-union.html>

<AnkiTags set-operations INTERSECT EXCEPT intermediate/>

# What is a CASE expression in SQL?

`CASE` is SQL's conditional expression — it returns a value based on evaluated conditions. There are two forms.

**Searched CASE** (more flexible — any boolean condition):

```sql
SELECT name, salary,
    CASE
        WHEN salary < 50000  THEN 'junior'
        WHEN salary < 100000 THEN 'mid'
        ELSE 'senior'
    END AS level
FROM employees;
```

**Simple CASE** (equality checks on one expression):

```sql
SELECT name,
    CASE status
        WHEN 'A' THEN 'Active'
        WHEN 'I' THEN 'Inactive'
        ELSE 'Unknown'
    END AS status_label
FROM employees;
```

`CASE` can be used anywhere an expression is valid: `SELECT`, `WHERE`, `ORDER BY`, inside aggregate functions.

**Source:** <https://www.postgresql.org/docs/current/functions-conditional.html#FUNCTIONS-CASE>

<AnkiTags CASE expressions junior intermediate/>

# How do you use CASE inside an aggregate function?

`CASE` inside `SUM` or `COUNT` is a powerful pattern for **conditional aggregation** — computing multiple subtotals in a single scan.

```sql
-- Count and sum broken down by status in one query
SELECT
    department,
    COUNT(*)                                        AS total,
    COUNT(CASE WHEN status = 'active' THEN 1 END)   AS active_count,
    COUNT(CASE WHEN status = 'left'   THEN 1 END)   AS left_count,
    SUM(CASE WHEN status = 'active' THEN salary ELSE 0 END) AS active_payroll
FROM employees
GROUP BY department;
```

`FILTER (WHERE condition)` is the modern SQL standard equivalent (PostgreSQL supports it):

```sql
COUNT(*) FILTER (WHERE status = 'active') AS active_count
```

**Source:** <https://www.postgresql.org/docs/current/sql-expressions.html#SYNTAX-AGGREGATES>

<AnkiTags CASE aggregate conditional-aggregation intermediate advanced/>

# What are common SQL string functions?

| Function                   | Example                     | Result    |
| -------------------------- | --------------------------- | --------- |
| `LENGTH(s)`                | `LENGTH('hello')`           | `5`       |
| `UPPER(s)`                 | `UPPER('sql')`              | `'SQL'`   |
| `LOWER(s)`                 | `LOWER('SQL')`              | `'sql'`   |
| `TRIM(s)`                  | `TRIM('  hi  ')`            | `'hi'`    |
| `LTRIM` / `RTRIM`          | `LTRIM('  hi')`             | `'hi'`    |
| `SUBSTRING(s,from,len)`    | `SUBSTRING('hello',2,3)`    | `'ell'`   |
| `REPLACE(s,from,to)`       | `REPLACE('hello','l','r')`  | `'herro'` |
| `CONCAT(s1,s2,...)`        | `CONCAT('a','b')`           | `'ab'`    |
| `\|\|` (operator)          | `'a' \|\| 'b'`              | `'ab'`    |
| `POSITION(sub IN s)`       | `POSITION('ll' IN 'hello')` | `3`       |
| `LEFT(s,n)` / `RIGHT(s,n)` | `LEFT('hello',2)`           | `'he'`    |
| `LPAD(s,n,pad)`            | `LPAD('7',3,'0')`           | `'007'`   |

Note: `SUBSTRING` syntax varies by dialect — MySQL uses `SUBSTR(s, pos, len)`.

**Source:** <https://www.postgresql.org/docs/current/functions-string.html>

<AnkiTags string-functions functions junior intermediate/>

# How does LIKE work, and what are its wildcards?

`LIKE` performs pattern matching on strings using two special wildcard characters:

- `%` — matches **any sequence** of zero or more characters
- `_` — matches **exactly one** character

```sql
SELECT name FROM employees WHERE name LIKE 'A%';     -- starts with A
SELECT name FROM employees WHERE name LIKE '%son';    -- ends with son
SELECT name FROM employees WHERE name LIKE '%an%';   -- contains 'an'
SELECT code FROM products  WHERE code LIKE 'P__-___'; -- P, 2 chars, dash, 3 chars

-- Case-insensitive (PostgreSQL):
SELECT name FROM employees WHERE name ILIKE 'alice%';

-- Escaping a literal %:
SELECT * FROM stats WHERE description LIKE '100\%' ESCAPE '\';
```

`LIKE` scans every row (full table scan) unless the pattern starts with a literal prefix — indexes can then be used for prefix matches.

**Source:** <https://www.postgresql.org/docs/current/functions-matching.html#FUNCTIONS-LIKE>

<AnkiTags string-functions LIKE wildcards junior/>

# How does SQL handle regular expressions?

PostgreSQL provides POSIX regular expression operators:

| Operator | Meaning                           |
| -------- | --------------------------------- |
| `~`      | Matches (case-sensitive)          |
| `~*`     | Matches (case-insensitive)        |
| `!~`     | Does not match (case-sensitive)   |
| `!~*`    | Does not match (case-insensitive) |

```sql
-- Emails matching a simple pattern
SELECT email FROM users WHERE email ~ '^[a-z0-9._%+\-]+@[a-z0-9.\-]+\.[a-z]{2,}$';

-- Extract with REGEXP_MATCH (returns array):
SELECT REGEXP_MATCH('2024-05-15', '(\d{4})-(\d{2})-(\d{2})');
-- {2024,05,15}

-- Replace with REGEXP_REPLACE:
SELECT REGEXP_REPLACE('hello   world', '\s+', ' ', 'g');
-- 'hello world'
```

MySQL uses `REGEXP` / `RLIKE` operators. Always prefer `LIKE` for simple patterns — regex has higher overhead.

**Source:** <https://www.postgresql.org/docs/current/functions-matching.html#FUNCTIONS-POSIX-REGEXP>

<AnkiTags string-functions regex REGEXP intermediate advanced/>

# What are common SQL date and time functions?

```sql
-- Current date/time
SELECT CURRENT_DATE;           -- date only:  2024-05-15
SELECT CURRENT_TIME;           -- time only:  14:30:00
SELECT CURRENT_TIMESTAMP;      -- both:       2024-05-15 14:30:00+00
SELECT NOW();                  -- same as CURRENT_TIMESTAMP (PostgreSQL)

-- Arithmetic
SELECT CURRENT_DATE + INTERVAL '30 days';   -- 30 days from now
SELECT AGE(TIMESTAMP '1990-06-15');          -- 34 years 11 mons ...

-- Extract parts
SELECT EXTRACT(YEAR  FROM CURRENT_TIMESTAMP);  -- 2024
SELECT EXTRACT(DOW   FROM CURRENT_DATE);       -- 0=Sun, 6=Sat
SELECT DATE_PART('month', order_date) AS month FROM orders;

-- Truncate
SELECT DATE_TRUNC('month', CURRENT_TIMESTAMP); -- 2024-05-01 00:00:00

-- Format
SELECT TO_CHAR(CURRENT_DATE, 'DD Mon YYYY');  -- '15 May 2024'
SELECT TO_DATE('15-05-2024', 'DD-MM-YYYY');   -- date value
```

**Source:** <https://www.postgresql.org/docs/current/functions-datetime.html>

<AnkiTags date-functions datetime functions intermediate/>

# What is DATE_TRUNC and when do you use it?

`DATE_TRUNC(precision, timestamp)` truncates a timestamp to the specified **precision** (year, quarter, month, week, day, hour, minute, second), zeroing out all less-significant parts.

```sql
SELECT DATE_TRUNC('month', '2024-05-15 14:30:00'::TIMESTAMP);
-- 2024-05-01 00:00:00

-- Count orders per month
SELECT
    DATE_TRUNC('month', order_date) AS month,
    COUNT(*)                        AS orders
FROM orders
GROUP  BY DATE_TRUNC('month', order_date)
ORDER  BY month;
```

This is the idiomatic PostgreSQL way to group time-series data by calendar period. MySQL equivalent: `DATE_FORMAT(order_date, '%Y-%m-01')` or `DATE_FORMAT` with `GROUP BY`.

**Source:** <https://www.postgresql.org/docs/current/functions-datetime.html#FUNCTIONS-DATETIME-TRUNC>

<AnkiTags date-functions DATE-TRUNC intermediate/>

# What is the difference between CHAR_LENGTH and LENGTH for multibyte strings?

`CHAR_LENGTH(s)` (alias: `CHARACTER_LENGTH`) counts the number of **characters** (Unicode code points).
`LENGTH(s)` in PostgreSQL counts the number of **characters** for text types but **bytes** for `bytea`.
`OCTET_LENGTH(s)` always counts **bytes**.

```sql
SELECT CHAR_LENGTH('hello');   -- 5
SELECT CHAR_LENGTH('héllo');   -- 5  (é is 1 character)
SELECT LENGTH('héllo');        -- 5  (PostgreSQL text: character count)
SELECT OCTET_LENGTH('héllo');  -- 6  (é is 2 bytes in UTF-8)
```

In MySQL, `LENGTH()` counts bytes and `CHAR_LENGTH()` counts characters — always prefer `CHAR_LENGTH` in MySQL for Unicode correctness.

**Source:** <https://www.postgresql.org/docs/current/functions-string.html>
<https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_char-length>

<AnkiTags string-functions LENGTH unicode intermediate/>

# What is an index, and what problem does it solve?

An index is a separate data structure that the database maintains alongside a table, allowing it to find rows matching a condition **without scanning every row** (a full table scan).

Without an index: finding `WHERE email = 'alice@example.com'` in a 10 million-row table requires reading all 10 million rows — O(n).

With a B-tree index on `email`: the database traverses a balanced tree in O(log n) steps.

```sql
CREATE INDEX idx_employees_email ON employees(email);

-- Unique index (also enforces constraint):
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Drop an index:
DROP INDEX idx_employees_email;
```

Indexes speed up reads but slow down writes (`INSERT`, `UPDATE`, `DELETE` must maintain the index). Don't index every column — index columns used in `WHERE`, `JOIN`, and `ORDER BY` clauses on large tables.

**Source:** <https://www.postgresql.org/docs/current/indexes-intro.html>

<AnkiTags indexes performance junior intermediate/>

# What is a B-tree index and why is it the default?

A **B-tree (Balanced Tree)** index organises key values in a sorted, self-balancing tree. All leaf nodes are at the same depth, giving consistent O(log n) performance for equality, range, and ordering operations.

```sql
-- Supports all of these predicates:
WHERE email = 'alice@example.com'        -- equality
WHERE salary BETWEEN 50000 AND 80000     -- range
WHERE name > 'M'                         -- greater-than
WHERE created_at < NOW() - INTERVAL '1 year' -- less-than
ORDER BY last_name                       -- sorted output
```

PostgreSQL also offers: `HASH` (equality only, faster), `GIN` (full-text, arrays, JSONB), `GiST` (geometric, full-text), `BRIN` (large physically ordered tables, very small index).

B-tree is the default because it covers the widest range of query patterns.

**Source:** <https://www.postgresql.org/docs/current/indexes-types.html>

<AnkiTags indexes B-tree performance intermediate advanced/>

# What is a composite index and why does column order matter?

A composite (multi-column) index covers multiple columns. The index is useful only if the query's `WHERE` or `JOIN` condition uses the **leading columns** of the index (the leftmost prefix rule).

```sql
CREATE INDEX idx_orders_cust_date ON orders(customer_id, order_date);

-- Uses the index:
WHERE customer_id = 5                          -- leading column
WHERE customer_id = 5 AND order_date > '2024-01-01'  -- both columns

-- Cannot use the index:
WHERE order_date > '2024-01-01'                -- skips leading column
```

**Column order rule:** Put the most selective column first for equality filters, then range-filter columns later. Equality columns first, range columns last.

**Source:** <https://www.postgresql.org/docs/current/indexes-multicolumn.html>

<AnkiTags indexes composite column-order intermediate advanced/>

# What is a covering index?

A **covering index** is an index that contains all the columns needed to satisfy a query — the database can answer the query **entirely from the index** without touching the main table (an "index-only scan").

```sql
-- Query needs: customer_id (filter), total_amount, order_date (output)
CREATE INDEX idx_orders_covering
    ON orders(customer_id, total_amount, order_date);

-- Index-only scan — no table access needed:
SELECT total_amount, order_date
FROM   orders
WHERE  customer_id = 42;
```

PostgreSQL uses the visibility map to confirm whether an index-only scan is possible. Adding extra columns to an index purely for coverage is a tradeoff: faster reads, larger index, slower writes.

**Source:** <https://www.postgresql.org/docs/current/indexes-index-only-scans.html>

<AnkiTags indexes covering index-only-scan advanced/>

# What is a partial index?

A **partial index** is built on a subset of rows — only those satisfying a `WHERE` predicate. It is smaller than a full index and only helps queries that match the same condition.

```sql
-- Only index active users (majority of queries filter on active)
CREATE INDEX idx_users_active_email
    ON users(email)
    WHERE status = 'active';

-- Only index unprocessed orders (usually a small fraction)
CREATE INDEX idx_orders_unprocessed
    ON orders(created_at)
    WHERE processed = FALSE;
```

Benefits: smaller index → faster writes, less storage, faster scans. Use when most queries filter on a specific condition and that subset is much smaller than the full table.

**Source:** <https://www.postgresql.org/docs/current/indexes-partial.html>

<AnkiTags indexes partial performance advanced/>

# When does an index NOT get used?

The query planner may skip an index even if one exists in these situations:

1. **Function on the indexed column**: `WHERE LOWER(email) = 'alice@example.com'` — the index on `email` is not used. Fix: create an expression index on `LOWER(email)`.
2. **Leading wildcard in LIKE**: `WHERE name LIKE '%smith'` — can't use a B-tree index. `LIKE 'smith%'` can.
3. **Type mismatch**: `WHERE id = '42'` when `id` is an integer — implicit cast may prevent index use.
4. **Low selectivity**: indexing a boolean column — the planner may prefer a sequential scan when >5–10% of rows match.
5. **Small table**: for very small tables a sequential scan is faster than index traversal.
6. **Non-leading composite column**: using the second column of a composite index without the first.

```sql
-- Fix function issue with an expression index:
CREATE INDEX idx_users_lower_email ON users(LOWER(email));
WHERE LOWER(email) = 'alice@example.com';  -- now uses index
```

**Source:** <https://www.postgresql.org/docs/current/indexes-expressional.html>
<https://www.postgresql.org/docs/current/indexes-partial.html>

<AnkiTags indexes performance pitfalls intermediate advanced/>

# How do you read a basic EXPLAIN output in PostgreSQL?

`EXPLAIN` shows the query plan without executing; `EXPLAIN ANALYZE` runs the query and shows actual vs. estimated row counts and timing.

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE customer_id = 42;
```

Key nodes to recognise:

| Node               | Meaning                                                      |
| ------------------ | ------------------------------------------------------------ |
| `Seq Scan`         | Full table scan — reads every row                            |
| `Index Scan`       | Traverses index, then fetches rows from table                |
| `Index Only Scan`  | Answers query entirely from index                            |
| `Bitmap Heap Scan` | Batches index lookups, then fetches heap in page order       |
| `Hash Join`        | Builds hash table on smaller input, probes with larger       |
| `Nested Loop`      | For each outer row, scans inner — good for small result sets |
| `Merge Join`       | Merges two pre-sorted inputs — good for large sorted sets    |

Look for: `rows=` estimate vs `actual rows=`, `cost=`, `loops=`, and `Buffers:` (cache hits vs. reads).

**Source:** <https://www.postgresql.org/docs/current/using-explain.html>

<AnkiTags EXPLAIN query-plan performance intermediate advanced/>

# What is a VIEW and what are its limitations?

A `VIEW` is a named, stored SQL query that can be referenced like a table. It does not store data (unless it is a materialized view).

```sql
CREATE VIEW active_employees AS
    SELECT id, name, department, salary
    FROM   employees
    WHERE  status = 'active';

SELECT * FROM active_employees WHERE department = 'Engineering';

-- Update the definition:
CREATE OR REPLACE VIEW active_employees AS ...;

-- Drop:
DROP VIEW active_employees;
```

**Limitations of plain views:**

- No data storage — re-executed on every query.
- Not all views are updatable (complex joins, aggregates, DISTINCT disqualify them).
- No index support (use a materialized view for that).

**Source:** <https://www.postgresql.org/docs/current/sql-createview.html>

<AnkiTags VIEW DDL intermediate/>

# What is a MATERIALIZED VIEW?

A materialized view stores the **result of a query physically on disk**, like a cached snapshot. Queries against it are fast (data is pre-computed), but it must be refreshed to reflect changes in the underlying tables.

```sql
CREATE MATERIALIZED VIEW monthly_sales_summary AS
    SELECT DATE_TRUNC('month', order_date) AS month,
           SUM(amount)                     AS total
    FROM   orders
    GROUP  BY 1;

-- Refresh the snapshot (blocks reads by default):
REFRESH MATERIALIZED VIEW monthly_sales_summary;

-- Non-blocking refresh (requires a UNIQUE index on the view):
REFRESH MATERIALIZED VIEW CONCURRENTLY monthly_sales_summary;
```

Use when: the underlying query is expensive, the data doesn't need to be real-time, and it is queried frequently.

**Source:** <https://www.postgresql.org/docs/current/sql-creatematerializedview.html>

<AnkiTags VIEW materialized-view DDL intermediate advanced/>

# What is the syntax for INSERT — single row, multiple rows, and INSERT INTO SELECT?

```sql
-- Single row:
INSERT INTO products (name, price, category)
VALUES ('Widget', 9.99, 'tools');

-- Multiple rows (one statement):
INSERT INTO products (name, price, category)
VALUES
    ('Gadget',  14.99, 'electronics'),
    ('Gizmo',   19.99, 'electronics'),
    ('Doohickey', 4.99, 'misc');

-- Insert from a query:
INSERT INTO archive_orders (order_id, customer_id, total, archived_at)
SELECT id, customer_id, total, NOW()
FROM   orders
WHERE  order_date < '2023-01-01';
```

Multi-row inserts and `INSERT INTO SELECT` are significantly faster than single-row inserts in a loop because they reduce round-trips and transaction overhead.

**Source:** <https://www.postgresql.org/docs/current/sql-insert.html>

<AnkiTags DML INSERT junior/>

# What is UPSERT (INSERT ON CONFLICT)?

UPSERT inserts a row if it doesn't exist, or updates it if a conflict (usually a unique constraint violation) occurs — atomically, without a separate SELECT check.

```sql
-- PostgreSQL: INSERT ... ON CONFLICT
INSERT INTO page_views (page_id, view_count)
VALUES (42, 1)
ON CONFLICT (page_id)
DO UPDATE SET view_count = page_views.view_count + 1;

-- Do nothing on conflict (idempotent insert):
INSERT INTO tags (name) VALUES ('python')
ON CONFLICT (name) DO NOTHING;

-- MySQL equivalent:
INSERT INTO page_views (page_id, view_count)
VALUES (42, 1)
ON DUPLICATE KEY UPDATE view_count = view_count + 1;
```

UPSERT avoids the **check-then-insert race condition** that occurs with a `SELECT` followed by an `INSERT`.

**Source:** <https://www.postgresql.org/docs/current/sql-insert.html#SQL-ON-CONFLICT>

<AnkiTags DML UPSERT INSERT ON-CONFLICT intermediate/>

# What is the UPDATE syntax and what is UPDATE FROM?

Basic UPDATE:

```sql
UPDATE employees
SET    salary = salary * 1.10,
       updated_at = NOW()
WHERE  department = 'Engineering'
  AND  performance_rating >= 4;
```

**UPDATE FROM** (PostgreSQL) — update rows based on a join to another table:

```sql
UPDATE employees e
SET    salary = e.salary * b.raise_pct
FROM   budget_allocations b
WHERE  b.department = e.department
  AND  b.year = 2024;
```

Always include a `WHERE` clause — omitting it updates **every row** in the table. Use `RETURNING` to see the updated rows:

```sql
UPDATE employees SET salary = salary * 1.1
WHERE id = 5
RETURNING id, name, salary;
```

**Source:** <https://www.postgresql.org/docs/current/sql-update.html>

<AnkiTags DML UPDATE junior intermediate/>

# What are SQL transactions and the basic transaction control statements?

A transaction groups multiple SQL statements into an **all-or-nothing unit**. Either all changes commit, or a rollback undoes everything.

```sql
BEGIN;                         -- start transaction

UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;

-- If something went wrong:
ROLLBACK;                      -- undo all changes since BEGIN

-- If everything is OK:
COMMIT;                        -- make changes permanent
```

**SAVEPOINT** lets you roll back to a point within a transaction without aborting the whole thing:

```sql
BEGIN;
INSERT INTO orders ...;
SAVEPOINT before_items;
INSERT INTO order_items ...;   -- this fails
ROLLBACK TO SAVEPOINT before_items;
-- order still exists, items rolled back
COMMIT;
```

**Source:** <https://www.postgresql.org/docs/current/tutorial-transactions.html>

<AnkiTags transactions TCL BEGIN COMMIT ROLLBACK junior intermediate/>

# What is a subquery in the SELECT list (scalar subquery)?

A scalar subquery in the `SELECT` list is a subquery that returns **exactly one row and one column**. It is evaluated for every row of the outer query — equivalent to a correlated subquery.

```sql
SELECT
    e.name,
    e.salary,
    (SELECT AVG(salary) FROM employees e2 WHERE e2.dept_id = e.dept_id)
        AS dept_avg_salary
FROM employees e;
```

If the subquery returns more than one row, SQL raises a runtime error. If it returns no rows, it returns `NULL`.

For performance, prefer a `JOIN` to a pre-aggregated subquery or a window function over a correlated scalar subquery in large-scale queries.

**Source:** <https://www.postgresql.org/docs/current/functions-subquery.html#FUNCTIONS-SUBQUERY-SCALAR>

<AnkiTags subqueries scalar SELECT intermediate/>

# What is a LATERAL join and when do you use it?

`LATERAL` allows a subquery in the `FROM` clause to reference columns from preceding `FROM` items — essentially a correlated subquery that can return multiple rows and columns.

```sql
-- For each customer, get their 3 most recent orders
SELECT c.name, recent.order_date, recent.total
FROM   customers c
JOIN   LATERAL (
    SELECT order_date, total
    FROM   orders o
    WHERE  o.customer_id = c.id     -- references c from outer FROM
    ORDER  BY order_date DESC
    LIMIT  3
) recent ON TRUE;
```

`LATERAL` is more powerful than a correlated scalar subquery because it can return multiple columns and rows. It is roughly equivalent to applying a function row-by-row, making it ideal for "top-N per group" queries.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-LATERAL>

<AnkiTags JOIN LATERAL subqueries advanced/>

# What is the difference between a correlated subquery and a JOIN, and when should you prefer each?

A **correlated subquery** re-executes for every row of the outer query — O(n × m) in the worst case.
A **JOIN** is planned holistically by the optimiser and typically results in a single pass with hash or merge strategies.

```sql
-- Correlated subquery (may scan employees once per dept row)
SELECT name FROM employees e
WHERE salary = (SELECT MAX(salary) FROM employees e2 WHERE e2.dept_id = e.dept_id);

-- Equivalent JOIN (optimiser can use a hash join once)
SELECT e.name FROM employees e
JOIN (
    SELECT dept_id, MAX(salary) AS max_sal
    FROM   employees
    GROUP  BY dept_id
) m ON e.dept_id = m.dept_id AND e.salary = m.max_sal;

-- Or with a window function (single scan):
SELECT name FROM (
    SELECT name, salary, MAX(salary) OVER (PARTITION BY dept_id) AS dept_max
    FROM employees
) t WHERE salary = dept_max;
```

Modern optimisers (PostgreSQL, SQL Server) can sometimes transform correlated subqueries into joins automatically, but writing the JOIN explicitly is safer and more portable.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html>

<AnkiTags subqueries JOIN performance intermediate advanced/>

# What does EXPLAIN ANALYZE tell you that EXPLAIN alone does not?

`EXPLAIN` shows the **estimated** query plan — the optimiser's prediction of costs, row counts, and strategy — without executing the query.

`EXPLAIN ANALYZE` **actually runs** the query and shows both estimated and **actual** values:

```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 42;

-- Sample output snippet:
-- Index Scan using idx_orders_customer on orders
--   (cost=0.43..8.45 rows=3 width=72)
--   (actual time=0.051..0.054 rows=3 loops=1)
--   Index Cond: (customer_id = 42)
-- Planning Time: 0.3 ms
-- Execution Time: 0.1 ms
```

Key things to look for:

- Large **rows estimated vs actual** discrepancy → stale statistics, run `ANALYZE table`.
- `loops=` > 1 on an expensive node → multiply cost by loop count.
- `Seq Scan` on a large table when you expected an index scan → missing/unused index.

**Source:** <https://www.postgresql.org/docs/current/using-explain.html>

<AnkiTags EXPLAIN query-plan performance intermediate advanced/>

# What is the difference between a sequential scan and an index scan?

|                | Sequential Scan                       | Index Scan                                        |
| -------------- | ------------------------------------- | ------------------------------------------------- |
| Strategy       | Reads entire table page by page       | Traverses B-tree, fetches matching heap pages     |
| When preferred | Small tables; fetching >5-10% of rows | Large tables; highly selective `WHERE` conditions |
| Cost           | O(n) table size                       | O(log n) + O(k) fetches (k = matching rows)       |
| Random I/O     | No                                    | Yes — can be slow if matching rows are scattered  |

```sql
-- Force a plan for testing (not for production):
SET enable_seqscan = OFF;
EXPLAIN SELECT * FROM orders WHERE customer_id = 42;
SET enable_seqscan = ON;
```

The planner chooses a seq scan over an index scan when the selectivity is low (many rows match), when the table fits in a few pages, or when page-level statistics suggest it is cheaper.

**Source:** <https://www.postgresql.org/docs/current/indexes-intro.html>

<AnkiTags indexes EXPLAIN performance intermediate advanced/>

# What is a stored procedure vs a function in SQL?

In standard SQL and PostgreSQL:

**Function** (`CREATE FUNCTION`): returns a value, can be used in `SELECT`, `WHERE`, etc. Should not have side effects (though can). Must return a type.

**Stored Procedure** (`CREATE PROCEDURE`, PostgreSQL 11+): does not return a value, called with `CALL`, can manage its own transactions (`COMMIT`/`ROLLBACK` inside the procedure).

```sql
-- Function: returns a value
CREATE FUNCTION get_employee_count(dept TEXT) RETURNS INT AS $$
    SELECT COUNT(*) FROM employees WHERE department = dept;
$$ LANGUAGE SQL;

SELECT get_employee_count('Engineering');   -- 42

-- Procedure: called with CALL, can contain transaction control
CREATE PROCEDURE archive_old_orders(cutoff DATE) AS $$
BEGIN
    INSERT INTO archive SELECT * FROM orders WHERE order_date < cutoff;
    DELETE FROM orders WHERE order_date < cutoff;
    COMMIT;
END;
$$ LANGUAGE plpgsql;

CALL archive_old_orders('2023-01-01');
```

**Source:** <https://www.postgresql.org/docs/current/sql-createprocedure.html>
<https://www.postgresql.org/docs/current/sql-createfunction.html>

<AnkiTags stored-procedures functions DDL advanced/>

# What is a trigger in SQL?

A trigger is a function that is automatically invoked by the database when a specified event (`INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`) occurs on a table or view.

```sql
-- 1. Create the trigger function
CREATE FUNCTION set_updated_at()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at := NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 2. Attach the trigger to a table
CREATE TRIGGER trg_employees_updated_at
BEFORE UPDATE ON employees
FOR EACH ROW EXECUTE FUNCTION set_updated_at();
```

Trigger timing: `BEFORE` (can modify the row) or `AFTER` (row already written). Granularity: `FOR EACH ROW` or `FOR EACH STATEMENT`. Use sparingly — hidden logic in triggers makes debugging harder.

**Source:** <https://www.postgresql.org/docs/current/sql-createtrigger.html>

<AnkiTags triggers DDL advanced/>

# Why can't you use a SELECT alias in a WHERE clause?

Because of SQL's **logical execution order** — `WHERE` is evaluated _before_ `SELECT`, so aliases defined in `SELECT` do not yet exist when `WHERE` runs.

```sql
-- WRONG: alias 'full_name' not yet defined at WHERE time
SELECT first_name || ' ' || last_name AS full_name
FROM   employees
WHERE  full_name LIKE 'Alice%';   -- ERROR: column "full_name" does not exist

-- FIX 1: repeat the expression
WHERE  first_name || ' ' || last_name LIKE 'Alice%';

-- FIX 2: wrap in a subquery or CTE
WITH named AS (
    SELECT *, first_name || ' ' || last_name AS full_name FROM employees
)
SELECT full_name FROM named WHERE full_name LIKE 'Alice%';
```

Exception: `ORDER BY` _can_ reference `SELECT` aliases in most databases because it executes after `SELECT`.

**Source:** <https://www.postgresql.org/docs/current/queries-overview.html>

<AnkiTags pitfalls execution-order aliases intermediate/>

# What happens when you JOIN on a column that contains NULLs?

`NULL` does not equal anything, including another `NULL`. In an `INNER JOIN`, rows where the join key is `NULL` on either side will **never match** and will be silently excluded.

```sql
-- employees.dept_id may be NULL for contractors
SELECT e.name, d.name
FROM   employees e
INNER  JOIN departments d ON e.dept_id = d.id;
-- Contractors (dept_id IS NULL) are excluded silently

-- Use LEFT JOIN to keep them:
SELECT e.name, d.name
FROM   employees e
LEFT   JOIN departments d ON e.dept_id = d.id;
-- Contractors appear with d.name = NULL
```

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN NULL pitfalls intermediate/>

# What does ORDER BY NULL FIRST / NULLS LAST do?

In SQL, the ordering of `NULL` values relative to non-NULL values is **implementation-defined** by default. PostgreSQL and many databases sort `NULL` as if it is **larger than any value** (so `NULLs LAST` ascending, `NULLs FIRST` descending). MySQL sorts `NULL` as smallest.

To make it explicit and portable:

```sql
-- ASC with NULLs at the end:
SELECT * FROM tasks ORDER BY due_date ASC NULLS LAST;

-- DESC with NULLs at the beginning:
SELECT * FROM tasks ORDER BY due_date DESC NULLS FIRST;

-- Default in PostgreSQL:
-- ASC  → NULLS LAST   (NULLs appear after all values)
-- DESC → NULLS FIRST  (NULLs appear before all values)
```

**Source:** <https://www.postgresql.org/docs/current/queries-order.html>

<AnkiTags ORDER-BY NULL NULLS-LAST intermediate/>

# What is the difference between NOT IN and NOT EXISTS when the subquery may contain NULLs?

`NOT IN (subquery)` returns **no rows at all** if the subquery contains even one `NULL` value, because `x NOT IN (1, NULL, 2)` evaluates as `x <> 1 AND x <> NULL AND x <> 2`. Since `x <> NULL` is `NULL` (unknown), the whole expression is `NULL` (falsy).

`NOT EXISTS` is immune to this — it only checks whether the subquery returns any rows.

```sql
-- BUG: returns no rows if dept_id is NULL in any subquery row
SELECT * FROM employees
WHERE dept_id NOT IN (SELECT id FROM departments WHERE active = FALSE);

-- SAFE: unaffected by NULLs
SELECT * FROM employees e
WHERE NOT EXISTS (
    SELECT 1 FROM departments d
    WHERE d.id = e.dept_id AND d.active = FALSE
);
```

**Source:** <https://www.postgresql.org/docs/current/functions-subquery.html#FUNCTIONS-SUBQUERY-EXISTS>

<AnkiTags NULL NOT-IN NOT-EXISTS pitfalls intermediate advanced/>

# What is the difference between UNION and a full outer join?

`UNION` stacks rows from two result sets **vertically** — both sides must have the same column count and compatible types. The result has the same columns but more rows.

A `FULL OUTER JOIN` combines two tables **horizontally** — it adds more columns, including NULLs where one side has no match.

```sql
-- UNION: same structure, different data
SELECT id, name FROM customers
UNION ALL
SELECT id, name FROM prospects;

-- FULL OUTER JOIN: combine related rows side by side
SELECT a.id, a.value AS val_a, b.value AS val_b
FROM   table_a a
FULL   OUTER JOIN table_b b ON a.id = b.id;
```

A `FULL OUTER JOIN` can also be expressed as `LEFT JOIN UNION ALL RIGHT JOIN WHERE left.key IS NULL` for databases that don't support `FULL OUTER JOIN` directly (MySQL before 8.0).

**Source:** <https://www.postgresql.org/docs/current/queries-union.html>
<https://www.postgresql.org/docs/current/queries-table-expressions.html>

<AnkiTags JOIN UNION set-operations intermediate/>

# How does SQL handle duplicate column names after a JOIN?

If two joined tables have columns with the same name, both appear in `SELECT *`. You must **qualify column names** with the table alias to avoid ambiguity.

```sql
-- Ambiguous: both tables have 'name' and 'created_at'
SELECT * FROM orders o JOIN customers c ON o.customer_id = c.id;
-- Result has two 'name' columns, two 'created_at' columns

-- Explicit — always prefer this:
SELECT o.id, o.created_at AS order_date,
       c.name AS customer_name, c.email
FROM   orders o
JOIN   customers c ON o.customer_id = c.id;
```

In application code, relying on `SELECT *` with joins often leads to bugs when table schemas change. Always name the columns you need.

**Source:** <https://www.postgresql.org/docs/current/queries-select-lists.html>

<AnkiTags JOIN SELECT pitfalls junior intermediate/>

# What is a NATURAL JOIN and why should you avoid it?

A `NATURAL JOIN` automatically joins two tables on **all columns that share the same name**. It is implicit — you don't specify the join condition.

```sql
SELECT * FROM employees NATURAL JOIN departments;
-- Automatically joins on any column with the same name in both tables
```

**Why to avoid it:**

- Adding a column with the same name to either table silently changes the join condition — a hidden schema-change bug.
- It is not obvious which columns are being used for joining when reading the query.
- Most style guides and linters discourage it; always use explicit `JOIN ... ON` or `JOIN ... USING`.

```sql
-- Prefer USING (explicit, single column):
SELECT * FROM employees JOIN departments USING (dept_id);
```

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN NATURAL-JOIN pitfalls intermediate/>

# What is GENERATE_SERIES and what are its uses?

`GENERATE_SERIES(start, stop, step)` is a PostgreSQL set-returning function that produces a sequence of values — integers, numerics, or timestamps.

```sql
-- Integers
SELECT * FROM GENERATE_SERIES(1, 5);        -- 1, 2, 3, 4, 5
SELECT * FROM GENERATE_SERIES(0, 10, 2);    -- 0, 2, 4, 6, 8, 10

-- Timestamps (daily series)
SELECT generate_series(
    '2024-01-01'::DATE,
    '2024-01-07'::DATE,
    '1 day'::INTERVAL
) AS day;

-- Fill gaps in time-series data (LEFT JOIN to include missing dates)
SELECT gs.day::DATE, COALESCE(COUNT(o.id), 0) AS orders
FROM   GENERATE_SERIES('2024-01-01', '2024-01-31', '1 day') gs(day)
LEFT   JOIN orders o ON o.order_date = gs.day::DATE
GROUP  BY gs.day
ORDER  BY gs.day;
```

**Source:** <https://www.postgresql.org/docs/current/functions-srf.html>

<AnkiTags functions GENERATE-SERIES PostgreSQL intermediate advanced/>

# What is COALESCE vs IFNULL vs NVL?

All three return the first non-NULL argument, but they have different portability:

| Function              | Database                     | Arguments  |
| --------------------- | ---------------------------- | ---------- |
| `COALESCE(a, b, ...)` | Standard SQL — all major DBs | Any number |
| `IFNULL(a, b)`        | MySQL, SQLite                | Exactly 2  |
| `NVL(a, b)`           | Oracle                       | Exactly 2  |
| `ISNULL(a, b)`        | SQL Server                   | Exactly 2  |

```sql
-- Standard SQL (works everywhere):
SELECT COALESCE(phone, mobile, 'N/A') AS contact FROM users;

-- MySQL shorthand:
SELECT IFNULL(phone, 'N/A') AS contact FROM users;
```

Prefer `COALESCE` for portability. It is also the only one that accepts more than two arguments.

**Source:** <https://www.postgresql.org/docs/current/functions-conditional.html#FUNCTIONS-COALESCE-NVL-IFNULL>
<https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#function_ifnull>

<AnkiTags NULL COALESCE dialect-differences intermediate/>

# What is the SQL MERGE statement?

`MERGE` performs insert, update, or delete on a target table based on whether rows match a source table — a single-statement UPSERT-plus-delete.

```sql
MERGE INTO employees AS target
USING staging_employees AS source
ON target.id = source.id

WHEN MATCHED AND source.action = 'UPDATE' THEN
    UPDATE SET salary = source.salary, dept_id = source.dept_id

WHEN MATCHED AND source.action = 'DELETE' THEN
    DELETE

WHEN NOT MATCHED THEN
    INSERT (id, name, salary, dept_id)
    VALUES (source.id, source.name, source.salary, source.dept_id);
```

`MERGE` is ANSI SQL. Support: SQL Server, Oracle, DB2 — natively. PostgreSQL added it in version 15. MySQL does not support `MERGE`; use `INSERT ... ON DUPLICATE KEY UPDATE` instead.

**Source:** <https://www.postgresql.org/docs/current/sql-merge.html>

<AnkiTags DML MERGE UPSERT advanced/>

# What are the SQL CAST and :: type cast operators?

`CAST(expr AS type)` is the ANSI SQL standard way to convert a value to a different data type. PostgreSQL also supports the shorthand `expr::type`.

```sql
-- ANSI standard (portable):
SELECT CAST('2024-05-15' AS DATE);
SELECT CAST(42 AS VARCHAR);
SELECT CAST(9.99 AS NUMERIC(10,2));

-- PostgreSQL shorthand:
SELECT '2024-05-15'::DATE;
SELECT 42::TEXT;
SELECT '3.14'::FLOAT;

-- Implicit casts can fail at runtime:
SELECT '2024-99-99'::DATE;   -- ERROR: invalid date

-- Safe cast (returns NULL instead of error — PostgreSQL 14+ or use TRY_CAST in SQL Server):
SELECT CASE WHEN value ~ '^\d+$' THEN value::INT ELSE NULL END FROM raw_data;
```

**Source:** <https://www.postgresql.org/docs/current/sql-expressions.html#SQL-SYNTAX-TYPE-CASTS>

<AnkiTags CAST type-conversion functions junior intermediate/>

# What are window function aggregates (SUM, AVG, COUNT OVER)?

Standard aggregate functions can be used as window functions by adding `OVER()`, turning them into running or grouped aggregations without collapsing rows.

```sql
SELECT
    order_date,
    amount,
    -- Running total (cumulative sum)
    SUM(amount) OVER (ORDER BY order_date
                      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)
        AS running_total,
    -- Rolling 7-day sum
    SUM(amount) OVER (ORDER BY order_date
                      ROWS BETWEEN 6 PRECEDING AND CURRENT ROW)
        AS rolling_7d,
    -- Department average without collapsing rows
    AVG(amount) OVER (PARTITION BY dept_id)
        AS dept_avg,
    -- Running count
    COUNT(*) OVER (PARTITION BY dept_id ORDER BY order_date)
        AS dept_cumulative_orders
FROM orders;
```

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions aggregate SUM AVG intermediate advanced/>

# What is the WINDOW clause in SQL?

The `WINDOW` clause lets you **name a window definition** and reuse it across multiple window functions, avoiding repetition.

```sql
SELECT
    name,
    salary,
    department,
    ROW_NUMBER() OVER dept_window,
    RANK()       OVER dept_window,
    AVG(salary)  OVER dept_window
FROM employees
WINDOW dept_window AS (
    PARTITION BY department
    ORDER BY salary DESC
);
```

Without `WINDOW`, you would repeat `PARTITION BY department ORDER BY salary DESC` for every function. It is especially useful when queries have 4+ window functions over the same partition.

**Source:** <https://www.postgresql.org/docs/current/sql-select.html#SQL-WINDOW>

<AnkiTags window-functions WINDOW intermediate advanced/>

# What is the difference between TRUNCATE and DROP?

`TRUNCATE` removes **all rows** from a table but keeps the table structure (schema, indexes, constraints).

`DROP TABLE` removes **the entire table** — structure, data, indexes, and constraints are all gone.

```sql
TRUNCATE TABLE logs;            -- table still exists, now empty
TRUNCATE TABLE orders, items;   -- truncate multiple tables atomically

DROP TABLE logs;                 -- table is completely gone
DROP TABLE IF EXISTS logs;       -- no error if table doesn't exist
DROP TABLE orders CASCADE;       -- drops dependent objects too (views, FKs)
```

`TRUNCATE` also resets sequences for `SERIAL`/`IDENTITY` columns in some databases (with `RESTART IDENTITY`). `DROP` cannot be rolled back in MySQL (DDL is implicit-commit), but can be rolled back in PostgreSQL.

**Source:** <https://www.postgresql.org/docs/current/sql-truncate.html>
<https://www.postgresql.org/docs/current/sql-droptable.html>

<AnkiTags DDL TRUNCATE DROP junior intermediate/>

# What is a schema in SQL?

A **schema** is a namespace within a database that organises database objects (tables, views, functions, indexes). It prevents naming conflicts and controls access.

```sql
-- Create a schema
CREATE SCHEMA reporting;

-- Create a table inside a schema
CREATE TABLE reporting.monthly_summary (
    month DATE, total NUMERIC
);

-- Query with schema qualification
SELECT * FROM reporting.monthly_summary;

-- Set search path (PostgreSQL): which schemas to check when unqualified
SET search_path TO reporting, public;
SELECT * FROM monthly_summary;   -- finds reporting.monthly_summary first
```

PostgreSQL's default schema is `public`. MySQL uses "schema" and "database" interchangeably. SQL Server separates catalog → schema → object.

**Source:** <https://www.postgresql.org/docs/current/ddl-schemas.html>

<AnkiTags DDL schema namespace intermediate/>

# What is the difference between a clustered and a non-clustered index?

A **clustered index** physically reorders the table's rows on disk to match the index order. There can be only one per table (because the rows can only be physically ordered one way). In SQL Server and MySQL/InnoDB, the primary key is automatically the clustered index.

A **non-clustered index** is a separate structure that stores the key values and a pointer back to the heap (or clustered key). A table can have many non-clustered indexes.

```sql
-- PostgreSQL does not have clustered indexes natively, but:
CLUSTER employees USING idx_employees_dept;  -- physically reorders once

-- MySQL: InnoDB always has a clustered index (the PK)
-- Every secondary index in InnoDB stores the PK value as its row pointer
```

In PostgreSQL all indexes are non-clustered by default. The `CLUSTER` command does a one-time physical sort but does not maintain order on future writes.

**Source:** <https://www.postgresql.org/docs/current/sql-cluster.html>
<https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html>

<AnkiTags indexes clustered non-clustered advanced/>

# What are the most common causes of slow SQL queries?

1. **Missing index** — `Seq Scan` on a large table for a selective `WHERE` condition.
2. **Wrong index** — index exists but not used (function on column, type mismatch, low selectivity).
3. **Too many indexes** — slows down writes; planner overhead.
4. **N+1 queries** — querying inside a loop; fix with a JOIN or batch query.
5. **`SELECT *`** — fetches unnecessary columns, prevents index-only scans.
6. **Large `OFFSET`** — `LIMIT 10 OFFSET 1000000` scans and discards 1 million rows; use keyset pagination instead.
7. **Stale statistics** — planner makes bad choices; run `ANALYZE`.
8. **Locking contention** — long-running transactions blocking writes.
9. **Unoptimised subqueries** — correlated subqueries instead of JOINs.
10. **Non-SARGable predicates** — `WHERE YEAR(created_at) = 2024` prevents index use; use `WHERE created_at BETWEEN '2024-01-01' AND '2024-12-31'`.

**Source:** <https://www.postgresql.org/docs/current/performance-tips.html>

<AnkiTags performance optimization pitfalls intermediate advanced/>

# What is keyset pagination and why is it better than LIMIT/OFFSET?

**OFFSET pagination** scans and discards all preceding rows on every page, making later pages progressively slower:

```sql
-- Page 1000 must scan 10,000 rows and discard 9,990 of them:
SELECT * FROM posts ORDER BY created_at DESC LIMIT 10 OFFSET 9990;
```

**Keyset pagination** (cursor-based) remembers the last seen value and uses it as a `WHERE` filter — the index is used efficiently for every page:

```sql
-- First page:
SELECT id, title, created_at FROM posts
ORDER BY created_at DESC, id DESC
LIMIT 10;

-- Next page (pass last seen created_at and id from previous page):
SELECT id, title, created_at FROM posts
WHERE (created_at, id) < ('2024-05-10 12:00:00', 4523)
ORDER BY created_at DESC, id DESC
LIMIT 10;
```

Keyset pagination is O(log n) per page using the index vs O(n) for `OFFSET`. Drawback: you cannot jump to an arbitrary page number.

**Source:** <https://www.postgresql.org/docs/current/queries-limit.html>

<AnkiTags performance pagination LIMIT OFFSET advanced/>

# What are SARGable predicates?

A **SARGable** (Search ARGument ABLE) predicate is one that the database engine can use an index to satisfy. Non-SARGable predicates force a full scan.

```sql
-- NON-SARGable: function wraps the indexed column
WHERE YEAR(order_date) = 2024                -- can't use index on order_date
WHERE LOWER(email) = 'alice@example.com'     -- can't use index on email
WHERE salary + 1000 > 60000                  -- can't use index on salary

-- SARGable rewrites:
WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01'
WHERE email = 'alice@example.com'            -- or create index on LOWER(email)
WHERE salary > 59000
```

Rule of thumb: **never apply a function to an indexed column in a `WHERE` clause**. Either rewrite the condition or create an expression index.

**Source:** <https://www.postgresql.org/docs/current/indexes-expressional.html>

<AnkiTags performance indexes SARGable pitfalls intermediate advanced/>

# What is the RETURNING clause?

`RETURNING` (PostgreSQL, SQLite 3.35+) returns values from rows that were inserted, updated, or deleted — without a separate `SELECT`.

```sql
-- Get the generated ID after INSERT
INSERT INTO users (name, email)
VALUES ('Alice', 'alice@example.com')
RETURNING id, created_at;

-- Get old and new values after UPDATE
UPDATE products
SET price = price * 0.9
WHERE category = 'sale'
RETURNING id, name, price AS new_price;

-- Get the deleted rows
DELETE FROM sessions
WHERE expires_at < NOW()
RETURNING session_id, user_id;
```

`RETURNING` avoids a second round-trip to the database and is safe because it reflects the actual final state of the row after triggers have fired.

**Source:** <https://www.postgresql.org/docs/current/dml-returning.html>

<AnkiTags DML RETURNING PostgreSQL intermediate/>

# What is the difference between NUMERIC(p,s) and FLOAT?

|           | `NUMERIC(p, s)`              | `FLOAT` / `DOUBLE`                  |
| --------- | ---------------------------- | ----------------------------------- |
| Storage   | Variable (exact)             | 4 or 8 bytes (approximate)          |
| Precision | Exact to specified scale     | IEEE 754 — limited binary precision |
| Speed     | Slower (software arithmetic) | Faster (hardware FPU)               |
| Use for   | Money, tax, audit            | Scientific, statistics              |

```sql
-- Float rounding error:
SELECT 0.1::FLOAT8 + 0.2::FLOAT8;
-- 0.30000000000000004

-- Numeric exact:
SELECT 0.1::NUMERIC + 0.2::NUMERIC;
-- 0.3

-- Real-world: never store prices as FLOAT
price NUMERIC(10, 2)  -- max 99,999,999.99, exact to the cent
```

**Source:** <https://www.postgresql.org/docs/current/datatype-numeric.html>

<AnkiTags datatypes NUMERIC FLOAT precision junior intermediate/>

# What is a CHECK constraint and how is it defined?

A `CHECK` constraint specifies a boolean condition that must be `TRUE` (or `NULL`) for every row. The constraint is evaluated on every `INSERT` and `UPDATE`.

```sql
CREATE TABLE products (
    id       SERIAL PRIMARY KEY,
    name     TEXT   NOT NULL,
    price    NUMERIC(10,2) CHECK (price >= 0),
    discount NUMERIC(5,2)  CHECK (discount BETWEEN 0 AND 100),
    sku      CHAR(8)        CHECK (sku ~ '^[A-Z]{2}-\d{4}-[A-Z]{2}$')
);

-- Table-level CHECK (can reference multiple columns):
CREATE TABLE events (
    start_time TIMESTAMPTZ NOT NULL,
    end_time   TIMESTAMPTZ NOT NULL,
    CONSTRAINT chk_event_times CHECK (end_time > start_time)
);

-- Add to existing table:
ALTER TABLE products ADD CONSTRAINT chk_price CHECK (price >= 0);
```

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-CHECK-CONSTRAINTS>

<AnkiTags DDL constraints CHECK junior intermediate/>

# What are SQL sequences and how do they relate to SERIAL?

A **sequence** is a database object that generates a monotonically increasing (or decreasing) series of integers. It is used to produce unique primary key values.

`SERIAL` in PostgreSQL is shorthand — it creates an integer column plus a sequence automatically:

```sql
-- These two are equivalent:
id SERIAL PRIMARY KEY;

id INTEGER NOT NULL DEFAULT nextval('table_id_seq') PRIMARY KEY;
-- Plus: CREATE SEQUENCE table_id_seq;
```

In PostgreSQL 10+, `IDENTITY` columns are preferred (SQL standard):

```sql
id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
-- or:
id INT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
```

Directly control a sequence:

```sql
SELECT nextval('my_seq');     -- advance and return next value
SELECT currval('my_seq');     -- return current value (same session)
SELECT setval('my_seq', 100); -- reset to 100
```

**Source:** <https://www.postgresql.org/docs/current/datatype-numeric.html#DATATYPE-SERIAL>
<https://www.postgresql.org/docs/current/sql-createsequence.html>

<AnkiTags DDL sequences SERIAL IDENTITY intermediate/>

# What is the DISTINCT ON clause (PostgreSQL)?

`DISTINCT ON (expressions)` keeps only the **first row** for each distinct value of the specified expressions. The "first" row is determined by `ORDER BY`, which must start with the same expressions.

```sql
-- Get each customer's most recent order
SELECT DISTINCT ON (customer_id)
    customer_id, order_id, order_date, total
FROM   orders
ORDER  BY customer_id, order_date DESC;
-- For each customer_id, returns the row with the latest order_date
```

This is a PostgreSQL extension (not standard SQL). The standard equivalent uses a `ROW_NUMBER()` window function or a correlated subquery. `DISTINCT ON` is often more readable and performs well with the right index.

**Source:** <https://www.postgresql.org/docs/current/queries-select-lists.html#QUERIES-DISTINCT>

<AnkiTags SELECT DISTINCT PostgreSQL intermediate advanced/>

# What is a foreign key and what referential integrity problem does it solve?

A `FOREIGN KEY` constraint ensures that a value in one table corresponds to an existing value in another table's primary (or unique) key column. It prevents **orphaned rows** — child rows that reference a parent row that no longer exists.

```sql
CREATE TABLE orders (
    id          SERIAL PRIMARY KEY,
    customer_id INT NOT NULL REFERENCES customers(id),
    amount      NUMERIC(10,2)
);

-- This INSERT fails if customer_id 999 doesn't exist in customers:
INSERT INTO orders (customer_id, amount) VALUES (999, 50.00);
-- ERROR: insert or update on table "orders" violates foreign key constraint

-- This DELETE fails if order rows reference customer 1:
DELETE FROM customers WHERE id = 1;
-- ERROR: update or delete on table "customers" violates foreign key constraint
```

Without FKs, you can end up with orders pointing at deleted customers — data integrity must then be enforced in application code, which is error-prone.

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-FK>

<AnkiTags DDL FOREIGN-KEY constraints referential-integrity junior/>

# What is the difference between INNER JOIN and a WHERE clause with multiple tables (implicit join)?

Both produce the same result, but the **explicit JOIN syntax** is strongly preferred.

```sql
-- Old implicit (comma) syntax — avoid:
SELECT e.name, d.name
FROM   employees e, departments d
WHERE  e.dept_id = d.id
  AND  e.status = 'active';

-- Modern explicit syntax:
SELECT e.name, d.name
FROM   employees e
INNER  JOIN departments d ON e.dept_id = d.id
WHERE  e.status = 'active';
```

The implicit syntax makes it easy to accidentally produce a **Cartesian product** by forgetting the join condition (only the `WHERE` filter remains). The explicit `JOIN ... ON` keeps join conditions separate from filter conditions, making queries easier to read and debug.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN implicit-join pitfalls junior intermediate/>

# What is GROUP BY ROLLUP and GROUP BY CUBE?

`ROLLUP` and `CUBE` are extensions to `GROUP BY` that generate **multiple levels of subtotals** in a single query.

`ROLLUP(a, b, c)` generates: `(a,b,c)`, `(a,b)`, `(a)`, `()` — a hierarchy of subtotals.
`CUBE(a, b)` generates all combinations: `(a,b)`, `(a)`, `(b)`, `()` — all possible subtotals.

```sql
SELECT
    region,
    product,
    SUM(sales) AS total
FROM   sales_data
GROUP  BY ROLLUP(region, product);
-- Rows for each (region, product), each region total, and grand total
-- NULL in a grouping column indicates it's a subtotal/grand total row
```

Use `GROUPING(col)` to distinguish real NULLs from subtotal NULLs (returns 1 for subtotal row).

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUPING-SETS>

<AnkiTags GROUP-BY ROLLUP CUBE advanced/>

# What is GROUPING SETS?

`GROUPING SETS` allows you to specify **exactly which combinations** of columns to group by in a single query — more flexible than `ROLLUP` or `CUBE`.

```sql
-- Equivalent to three separate GROUP BY queries UNIONed together:
SELECT region, product, channel, SUM(sales)
FROM   sales
GROUP  BY GROUPING SETS (
    (region, product),   -- subtotal by region+product
    (region),            -- subtotal by region only
    ()                   -- grand total
);
```

`ROLLUP(a, b)` is syntactic sugar for `GROUPING SETS ((a,b),(a),())`.
`CUBE(a, b)` is syntactic sugar for `GROUPING SETS ((a,b),(a),(b),())`.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUPING-SETS>

<AnkiTags GROUP-BY GROUPING-SETS advanced/>

# How do you pivot data in SQL (rows to columns)?

SQL does not have a built-in `PIVOT` statement in standard SQL or PostgreSQL, but conditional aggregation achieves the same result:

```sql
-- Unpivoted: (year, quarter, revenue)
-- Pivoted: one column per quarter

SELECT
    year,
    SUM(CASE WHEN quarter = 1 THEN revenue END) AS q1,
    SUM(CASE WHEN quarter = 2 THEN revenue END) AS q2,
    SUM(CASE WHEN quarter = 3 THEN revenue END) AS q3,
    SUM(CASE WHEN quarter = 4 THEN revenue END) AS q4
FROM quarterly_revenue
GROUP BY year
ORDER BY year;
```

SQL Server supports `PIVOT` / `UNPIVOT` syntax natively. PostgreSQL's `crosstab()` function (in the `tablefunc` extension) also handles pivoting.

**Source:** <https://www.postgresql.org/docs/current/tablefunc.html>

<AnkiTags pivot conditional-aggregation CASE advanced/>

# What is the FILTER clause in aggregate functions?

`FILTER (WHERE condition)` is a SQL standard clause (PostgreSQL, SQLite) that applies a condition to rows **before** they are passed to the aggregate. It is cleaner than a `CASE` inside the aggregate.

```sql
-- Using CASE (traditional):
SELECT
    COUNT(CASE WHEN status = 'active' THEN 1 END) AS active_count,
    AVG(CASE WHEN status = 'active' THEN salary END) AS active_avg_sal
FROM employees;

-- Using FILTER (cleaner, standard SQL):
SELECT
    COUNT(*) FILTER (WHERE status = 'active') AS active_count,
    AVG(salary) FILTER (WHERE status = 'active') AS active_avg_sal
FROM employees;
```

`FILTER` also works with window functions in PostgreSQL.

**Source:** <https://www.postgresql.org/docs/current/sql-expressions.html#SYNTAX-AGGREGATES>

<AnkiTags aggregate FILTER CASE intermediate advanced/>

# What is a FULL TEXT SEARCH in SQL and how do you use it in PostgreSQL?

Full-text search finds documents matching a query based on word stems and language rules — more powerful than `LIKE` for natural language.

```sql
-- Store a tsvector (precomputed search index):
ALTER TABLE articles ADD COLUMN search_vec TSVECTOR
    GENERATED ALWAYS AS (to_tsvector('english', title || ' ' || body)) STORED;

CREATE INDEX idx_articles_fts ON articles USING GIN(search_vec);

-- Search using tsquery:
SELECT title FROM articles
WHERE  search_vec @@ to_tsquery('english', 'python & tutorial');

-- Phrase search:
WHERE  search_vec @@ phraseto_tsquery('english', 'machine learning');

-- Highlight matches:
SELECT ts_headline('english', body, to_tsquery('python')) FROM articles ...;
```

`GIN` indexes are optimised for `tsvector` (many values per row).

**Source:** <https://www.postgresql.org/docs/current/textsearch.html>

<AnkiTags full-text-search GIN tsvector PostgreSQL advanced/>

# What are JSONB and JSON column types in PostgreSQL?

PostgreSQL supports two JSON types:

- `JSON`: stores the raw JSON text as-is, validating it on insert.
- `JSONB`: stores JSON in a **decomposed binary format** — parsed on insert, supports indexing, faster to query.

Prefer `JSONB` for almost all use cases.

```sql
-- Create a table with JSONB
CREATE TABLE events (
    id      SERIAL PRIMARY KEY,
    payload JSONB NOT NULL
);

-- Insert
INSERT INTO events (payload) VALUES ('{"type":"click","x":10,"y":20}');

-- Query with operators
SELECT * FROM events WHERE payload->>'type' = 'click';
SELECT payload->'x' FROM events;           -- returns JSON value
SELECT payload->>'x' FROM events;          -- returns text value

-- Filter nested key
SELECT * FROM events WHERE payload @> '{"type":"click"}';

-- Index for fast key lookups
CREATE INDEX idx_events_payload ON events USING GIN(payload);
```

**Source:** <https://www.postgresql.org/docs/current/datatype-json.html>

<AnkiTags JSONB JSON datatypes PostgreSQL advanced/>

# What is the difference between TIMESTAMP and TIMESTAMPTZ?

`TIMESTAMP` (without time zone) stores a date and time with no timezone information. It represents a local clock time with no reference to UTC.

`TIMESTAMPTZ` (with time zone) stores a UTC instant. When written, it converts the input to UTC; when read, it converts back to the session's `TimeZone` setting.

```sql
SET TimeZone = 'America/New_York';

INSERT INTO events (ts, tstz) VALUES (NOW(), NOW());
-- ts:   '2024-05-15 10:00:00'   (local time stored, no conversion)
-- tstz: '2024-05-15 14:00:00+00' (UTC stored)

SET TimeZone = 'Asia/Tokyo';
SELECT ts, tstz FROM events;
-- ts:   '2024-05-15 10:00:00'      (unchanged — no zone info)
-- tstz: '2024-05-15 23:00:00+09'   (correctly converted to Tokyo time)
```

**Always use `TIMESTAMPTZ`** for application data. `TIMESTAMP` is only appropriate for abstract local times (e.g., recurring schedules) where timezone conversion is undesirable.

**Source:** <https://www.postgresql.org/docs/current/datatype-datetime.html>

<AnkiTags datatypes TIMESTAMP timezone intermediate advanced/>

# What are SQL window function aggregates vs analytic functions?

Window functions fall into two categories:

**Aggregate window functions** — standard aggregates used with `OVER()`:

```sql
SUM(salary)    OVER (PARTITION BY dept_id)
AVG(salary)    OVER (PARTITION BY dept_id ORDER BY hire_date)
COUNT(*)       OVER (PARTITION BY dept_id)
MIN/MAX(salary) OVER (PARTITION BY dept_id)
```

**Analytic (ranking/navigation) functions** — purpose-built for ordered sets:

```sql
ROW_NUMBER() OVER (...)      -- unique sequential number
RANK()       OVER (...)      -- rank with gaps on ties
DENSE_RANK() OVER (...)      -- rank without gaps
PERCENT_RANK() OVER (...)    -- relative rank 0.0–1.0
CUME_DIST()  OVER (...)      -- cumulative distribution 0.0–1.0
NTILE(n)     OVER (...)      -- bucket number
LAG/LEAD(col, n) OVER (...)  -- access adjacent rows
FIRST_VALUE/LAST_VALUE/NTH_VALUE(col, n) OVER (...)
```

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions aggregate analytic intermediate advanced/>

# How do you find the second (or Nth) highest salary in SQL?

A classic interview question with several valid approaches:

```sql
-- Using OFFSET (simple but not scalable):
SELECT DISTINCT salary FROM employees
ORDER BY salary DESC LIMIT 1 OFFSET 1;

-- Using a subquery:
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Using DENSE_RANK (handles ties correctly, generalises to Nth):
SELECT salary FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM   employees
) t
WHERE rnk = 2;

-- Nth highest with a parameter:
-- Replace rnk = 2 with rnk = N
```

The `DENSE_RANK` approach is preferred in interviews because it handles ties correctly and generalises to any rank N.

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions RANK interview-classic intermediate/>

# What is the difference between PERCENT_RANK and CUME_DIST?

Both window functions return a value between 0 and 1 indicating a row's position within an ordered partition.

`PERCENT_RANK` = (rank - 1) / (total rows - 1) — the first row is always 0.0.
`CUME_DIST` = rows with value ≤ current value / total rows — always between 1/n and 1.

```sql
SELECT name, score,
    PERCENT_RANK() OVER (ORDER BY score) AS pct_rank,
    CUME_DIST()    OVER (ORDER BY score) AS cume_dist
FROM exam_results;

-- score | pct_rank | cume_dist
--    60 |   0.000  |   0.250   (1 of 4 rows ≤ 60)
--    70 |   0.333  |   0.500
--    80 |   0.667  |   0.750
--    90 |   1.000  |   1.000
```

`CUME_DIST` tells you: "what fraction of rows have a value ≤ this row's value?"

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>

<AnkiTags window-functions PERCENT-RANK CUME-DIST advanced/>

# What is the ANY and ALL operator in SQL subqueries?

`ANY` (synonym: `SOME`) returns `TRUE` if the comparison is true for **at least one** value in the subquery.
`ALL` returns `TRUE` if the comparison is true for **every** value in the subquery.

```sql
-- Employees earning more than at least one person in dept 5:
SELECT name FROM employees
WHERE salary > ANY (SELECT salary FROM employees WHERE dept_id = 5);

-- Employees earning more than everyone in dept 5:
SELECT name FROM employees
WHERE salary > ALL (SELECT salary FROM employees WHERE dept_id = 5);
```

`= ANY(subquery)` is equivalent to `IN (subquery)`.
`<> ALL(subquery)` is equivalent to `NOT IN (subquery)` — but inherits the same NULL trap as `NOT IN`.

**Source:** <https://www.postgresql.org/docs/current/functions-subquery.html#FUNCTIONS-SUBQUERY-ANY-SOME>

<AnkiTags subqueries ANY ALL operators intermediate/>

# What is an expression index in PostgreSQL?

An **expression index** (also called a functional index) is built on the result of an expression or function applied to column values, rather than on the raw column values.

```sql
-- Without expression index, this requires a full scan:
WHERE LOWER(email) = 'alice@example.com'

-- Create an expression index:
CREATE INDEX idx_users_lower_email ON users(LOWER(email));

-- Now this uses the index:
WHERE LOWER(email) = 'alice@example.com'

-- Date-based expression index:
CREATE INDEX idx_orders_year ON orders(EXTRACT(YEAR FROM order_date));
WHERE EXTRACT(YEAR FROM order_date) = 2024
```

The index stores the pre-computed expression values, so queries using that exact expression can use the index. The database automatically maintains the index as data changes.

**Source:** <https://www.postgresql.org/docs/current/indexes-expressional.html>

<AnkiTags indexes expression-index performance advanced/>

# What is the USING clause in a JOIN?

`JOIN ... USING (column)` is shorthand for `JOIN ... ON table1.column = table2.column`. It merges the specified column into a single column in the output (unlike `ON`, which keeps both).

```sql
-- USING — column appears once in result:
SELECT * FROM employees JOIN departments USING (dept_id);

-- ON — both dept_id columns appear in result:
SELECT * FROM employees e JOIN departments d ON e.dept_id = d.dept_id;
```

`USING` only works when the join column has the **same name** in both tables. It is cleaner but `ON` is more explicit and necessary for unequal column names or complex conditions.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-JOIN>

<AnkiTags JOIN USING intermediate/>

# What SQL techniques help with "top-N per group" queries?

**Top-N per group** (e.g., top 3 products per category by sales) is a classic SQL problem.

**Method 1 — ROW_NUMBER window function (most common):**

```sql
SELECT category, product, sales
FROM (
    SELECT category, product, sales,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY sales DESC) AS rn
    FROM   products
) t
WHERE rn <= 3;
```

**Method 2 — LATERAL JOIN:**

```sql
SELECT c.name, top_p.product, top_p.sales
FROM   categories c
JOIN   LATERAL (
    SELECT product, sales FROM products p
    WHERE  p.category_id = c.id
    ORDER  BY sales DESC LIMIT 3
) top_p ON TRUE;
```

**Method 3 — Correlated subquery (less efficient):**

```sql
SELECT category, product, sales FROM products p1
WHERE (SELECT COUNT(*) FROM products p2
       WHERE p2.category = p1.category AND p2.sales > p1.sales) < 3;
```

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>
<https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-LATERAL>

<AnkiTags window-functions ROW-NUMBER LATERAL interview-classic advanced/>

# What are the GRANT and REVOKE statements?

`GRANT` gives a user or role permissions on a database object. `REVOKE` removes them.

```sql
-- Grant SELECT on a table to a user
GRANT SELECT ON employees TO analyst_user;

-- Grant multiple privileges
GRANT SELECT, INSERT, UPDATE ON orders TO app_user;

-- Grant all privileges
GRANT ALL PRIVILEGES ON TABLE products TO admin_user;

-- Grant on all tables in a schema
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly_role;

-- Grant a role to a user
GRANT reporting_role TO alice;

-- Revoke
REVOKE INSERT ON orders FROM app_user;
REVOKE ALL PRIVILEGES ON TABLE products FROM old_user;
```

Privileges include: `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, `REFERENCES`, `TRIGGER`, `USAGE`, `EXECUTE`, `CREATE`.

**Source:** <https://www.postgresql.org/docs/current/sql-grant.html>

<AnkiTags DCL GRANT REVOKE permissions intermediate/>

# What is the HAVING clause used for beyond simple COUNT filters?

`HAVING` filters aggregated groups using any aggregate expression, including complex conditions and multiple aggregates:

```sql
-- Groups where average salary is above threshold AND max salary is below a cap:
SELECT dept_id,
       AVG(salary) AS avg_sal,
       MAX(salary) AS max_sal
FROM   employees
GROUP  BY dept_id
HAVING AVG(salary) > 60000
   AND MAX(salary) < 200000;

-- Departments where the ratio of active to total is above 80%:
HAVING COUNT(*) FILTER (WHERE status = 'active')::FLOAT
       / NULLIF(COUNT(*), 0) > 0.8;

-- Groups with more than one unique job title:
HAVING COUNT(DISTINCT job_title) > 1;
```

`HAVING` can also reference expressions from `GROUP BY` directly.

**Source:** <https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUP>

<AnkiTags HAVING GROUP-BY aggregate intermediate advanced/>

# How do you delete duplicate rows in SQL while keeping one copy?

Use a CTE with `ROW_NUMBER()` to identify duplicates, then delete all but the one with the lowest (or highest) `rowid`/`ctid`:

```sql
-- PostgreSQL: delete duplicates, keep the row with the smallest ctid
WITH dupes AS (
    SELECT ctid,
           ROW_NUMBER() OVER (
               PARTITION BY email           -- duplicate key
               ORDER BY ctid               -- keep first physical row
           ) AS rn
    FROM users
)
DELETE FROM users
WHERE ctid IN (SELECT ctid FROM dupes WHERE rn > 1);
```

Alternative using a self-join (works in most databases):

```sql
DELETE FROM users a
USING users b
WHERE a.email = b.email
  AND a.id > b.id;    -- keep the row with the smaller id
```

**Source:** <https://www.postgresql.org/docs/current/functions-window.html>
<https://www.postgresql.org/docs/current/ddl-system-columns.html>

<AnkiTags DML duplicates ROW-NUMBER interview-classic advanced/>

# SQL — UNION

Practice exercises covering `UNION ALL` — combining the results of two separate `SELECT` queries into one list, tagging each half with a literal status column, and sorting the combined result.

## Schema

Tasks 1–2 use two new tables. Task 3 reuses `products` and `order_items` from [join.md](./join.md).

```sql
CREATE TABLE users1 (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    is_active BOOLEAN DEFAULT TRUE
);

INSERT INTO users1 (id, name, is_active) VALUES
    (1, 'Bob', TRUE),
    (2, 'Dima', FALSE),
    (3, 'Vadim', TRUE);

CREATE TABLE orderss (
    id INT PRIMARY KEY,
    user_id INT NOT NULL,
    order_date DATE,
    status VARCHAR(30)
);

INSERT INTO orderss (id, user_id, order_date, status) VALUES
    (1, 1, '2023-11-05', 'paid'),
    (2, 2, '2023-12-20', 'cancel'),
    (3, 3, '2024-01-01', 'paid'),
    (4, 1, '2024-02-15', 'paid'),
    (5, 2, '2024-03-10', 'created'),
    (6, 3, '2023-06-30', 'paid');
```

---

## Task 1 — Active and inactive users in one list

**Goal:** produce one export for a CRM containing both active and inactive users, each tagged with its status.

**Fields:** id, name, user_status

```sql
SELECT id, name, 'active' AS user_status
FROM users1
WHERE is_active = TRUE

UNION ALL

SELECT id, name, 'inactive' AS user_status
FROM users1
WHERE is_active = FALSE

ORDER BY name;
```

**Notes:**
- `is_active` only stores `TRUE`/`FALSE`. `'active'`/`'inactive'` in `SELECT` aren't read from any column — they're literal text values written directly into the query, applied to every row each `SELECT` returns. `WHERE` and `SELECT` are independent: `WHERE` decides which rows qualify, `SELECT` decides what to display for them — the text has to be written consistently with the filter by hand, the database doesn't check that they match.
- `UNION ALL` (not `UNION`) is safe here because the two halves are mutually exclusive (`is_active = TRUE` vs `= FALSE`) — no real duplicate rows can occur, so there's no need for `UNION`'s extra duplicate-checking work.
- `ORDER BY` is written once, after both queries — it sorts the already-combined result, not each half separately.

---

## Task 2 — Orders before and after a cutoff date, in one feed

**Goal:** see which orders happened before a new site version launched and which came after.

**Fields:** order_id, user_id, order_date, period

```sql
SELECT id AS order_id, user_id, order_date, 'old' AS period
FROM orderss
WHERE order_date < '2024-01-01'

UNION ALL

SELECT id AS order_id, user_id, order_date, 'new' AS period
FROM orderss
WHERE order_date >= '2024-01-01'

ORDER BY order_date, order_id;
```

**Notes:**
- Same pattern as Task 1, applied to a date boundary instead of a boolean flag: two mutually exclusive `WHERE` conditions (`<` vs `>=`), each tagged with its own literal `period` value.
- `>=` on the "new" side means the cutoff date itself (`2024-01-01`) counts as new, not old.
- Sorting by two columns (`order_date, order_id`) breaks ties on the same date using order_id, same idea as multi-column sorting used elsewhere.

---

## Task 3 — Products that have and haven't been ordered

**Goal:** distinguish products that sell from ones sitting unsold.

**Fields:** product_id, product_name, product_status

```sql
SELECT DISTINCT p.id AS product_id, p.name AS product_name, 'ordered' AS product_status
FROM products p
JOIN order_items oi ON oi.product_id = p.id

UNION ALL

SELECT p.id AS product_id, p.name AS product_name, 'never_ordered' AS product_status
FROM products p
LEFT JOIN order_items oi ON oi.product_id = p.id
WHERE oi.product_id IS NULL

ORDER BY product_name;
```

**Notes:**
- `p` / `oi` are table aliases — short names for `products` and `order_items` so they don't have to be spelled out on every column reference.
- `DISTINCT` matters only in the first half: a product bought in several separate orders would otherwise appear once per order line. The second half doesn't need it, since a product missing from `order_items` can only produce one unmatched row per product.
- The "never ordered" half reuses the standard orphan-finding pattern (`LEFT JOIN` + `IS NULL`) from the join practice — in the current dataset every product has been ordered at least once, so this half returns 0 rows, and the combined result is just the four ordered products.
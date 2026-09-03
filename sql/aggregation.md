# SQL — Aggregation (GROUP BY, HAVING)

Practice exercises covering aggregate functions (`COUNT`, `SUM`), grouping rows with `GROUP BY`, and filtering groups with `HAVING` as opposed to filtering rows with `WHERE`.

## Schema

Tasks 1 and 5 reuse `products`, Tasks 2, 3 and 6 reuse `order_items`/`orders` — all defined in [join.md](./join.md). Task 4 uses a new table.

```sql
CREATE TABLE users2 (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    country VARCHAR(50),
    is_active BOOLEAN
);

INSERT INTO users2 (id, name, country, is_active) VALUES
    (1, 'Alice', 'KZ', TRUE),
    (2, 'Bob', 'USA', TRUE),
    (3, 'Charlie', 'RU', TRUE),
    (4, 'Diana', 'KZ', TRUE),
    (5, 'Frank', 'RU', FALSE),
    (6, 'Grace', 'USA', FALSE),
    (7, 'Helen', 'KZ', FALSE),
    (8, 'Ivan', 'RU', TRUE);
```

---

## Task 1 — Product count per category

**Goal:** see how many products fall into each category.

**Fields:** category, products_count

```sql
SELECT category, COUNT(*) AS products_count
FROM products
GROUP BY category
ORDER BY products_count DESC;
```

**Notes:**
- `GROUP BY category` splits all rows into buckets by category first; `COUNT(*)` then counts rows separately within each bucket, instead of counting the whole table at once.
- Dropping `ORDER BY` wouldn't change which rows come back or their counts — only the order they're displayed in. Without it, the result isn't guaranteed to come back in any particular order, so a person reading the report would have to scan every row to find the largest category instead of seeing it immediately at the top.

---

## Task 2 — Revenue per order

**Goal:** for every order, calculate the total quantity of items and the total revenue.

**Fields:** order_id, total_quantity, order_revenue

```sql
SELECT
    order_id,
    SUM(quantity) AS total_quantity,
    SUM(quantity * price_at_purchase) AS order_revenue
FROM order_items
GROUP BY order_id
ORDER BY order_revenue DESC;
```

**Notes:**
- `SUM(quantity * price_at_purchase)` first computes the line total for each row (quantity × price), then adds those line totals together within each order group.
- Adding a column like `product_id` to `SELECT` without also adding it to `GROUP BY` or wrapping it in an aggregate isn't allowed: order 101 has two different `product_id` values in the raw data, so once rows are grouped only by `order_id`, there's no single correct `product_id` left to display for that group — SQL has no way to pick one.

---

## Task 3 — Revenue from paid orders only

**Goal:** calculate revenue, counting only orders with `status = 'paid'`.

**Fields:** order_id, order_revenue

```sql
SELECT
    order_items.order_id,
    SUM(order_items.quantity * order_items.price_at_purchase) AS order_revenue
FROM order_items
JOIN orders ON order_items.order_id = orders.id
WHERE orders.status = 'paid'
GROUP BY order_items.order_id
ORDER BY order_revenue DESC;
```

**Notes:**
- `WHERE` removes non-paid rows before grouping happens, so cancelled/created orders never contribute to any `SUM`.
- A real reason to look only at paid orders: revenue reporting. Counting `created` or `cancelled` orders in a revenue total would overstate money actually received — those orders may never be paid for at all.

---

## Task 4 — Countries with enough active users

**Goal:** find countries with a meaningful number of active users, not just one-off users.

**Fields:** country, active_users_count

```sql
SELECT country, COUNT(*) AS active_users_count
FROM users2
WHERE is_active = TRUE
GROUP BY country
HAVING COUNT(*) >= 2
ORDER BY active_users_count DESC;
```

**Notes:**
- `is_active = TRUE` belongs in `WHERE`, not `HAVING`, because it's a property of an individual row (a single user), and can be checked before any grouping happens. `HAVING COUNT(*) >= 2` belongs after grouping, because "how many active users a country has" only exists once the rows have already been grouped and counted — there's no per-row value to filter on.
- Without the `WHERE` filter, `COUNT(*)` would count every user per country regardless of activity, which isn't what the task asks for.

---

## Task 5 — Best-selling products

**Goal:** find which products sell the most units.

**Fields:** product_name, total_quantity_sold

```sql
SELECT
    products.name AS product_name,
    SUM(order_items.quantity) AS total_quantity_sold
FROM order_items
JOIN products ON order_items.product_id = products.id
GROUP BY products.name
ORDER BY total_quantity_sold DESC
LIMIT 3;
```

**Notes:**
- Grouping by `products.name` (rather than by `order_id`, as in Task 2) rolls up quantities sold across every order a product appears in, giving one total per product instead of one per order.
- `LIMIT 3` after `ORDER BY DESC` gives the top 3 — sorting has to come first, or `LIMIT` would just cut off an arbitrary 3 rows.

---

## Task 6 — Orders with unusually low revenue

**Goal:** flag orders whose total revenue looks too small.

**Fields:** order_id, order_revenue

```sql
SELECT
    order_id,
    SUM(quantity * price_at_purchase) AS order_revenue
FROM order_items
GROUP BY order_id
HAVING SUM(quantity * price_at_purchase) < 100
ORDER BY order_revenue ASC;
```

**Notes:**
- The revenue threshold is checked with `HAVING`, not `WHERE`, since `order_revenue` only exists as a per-group total after `SUM` runs — there's no single-row `order_revenue` value to filter before grouping.
- The `SUM(...)` expression is repeated in `HAVING` rather than referencing the `order_revenue` alias directly, since not every SQL engine allows a `SELECT` alias to be reused inside `HAVING`.
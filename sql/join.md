# SQL — JOINs

Practice exercises covering `INNER JOIN`, `LEFT JOIN`, join conditions vs. `WHERE` filtering, and using `IS NULL` to detect missing relationships — based on a small e-commerce schema (users, products, orders, order items).

## Schema

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    country VARCHAR(50)
);

INSERT INTO users (id, name, country) VALUES
    (1, 'Alice', 'KZ'),
    (2, 'Bob', 'USA'),
    (3, 'Charlie', 'RU');

CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    category VARCHAR(100),
    price DECIMAL(10,2)
);

INSERT INTO products (id, name, category, price) VALUES
    (1, 'Mi Band 7', 'smartwatch', 49.00),
    (2, 'Surface Pro', 'tablet', 1299.00),
    (3, 'iPhone 15', 'smartphone', 999.00),
    (4, 'Logitech MX Master', 'mouse', 120.00);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT NOT NULL,
    order_date DATE,
    status VARCHAR(30),
    FOREIGN KEY (user_id) REFERENCES users(id)
);

INSERT INTO orders (id, user_id, order_date, status) VALUES
    (101, 1, '2024-02-01', 'paid'),
    (102, 1, '2024-02-03', 'created'),
    (103, 2, '2024-02-05', 'cancelled');

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    price_at_purchase DECIMAL(10,2),
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

INSERT INTO order_items (order_id, product_id, quantity, price_at_purchase) VALUES
    (101, 3, 1, 999.00),
    (101, 4, 1, 120.00),
    (102, 1, 2, 49.00),
    (103, 2, 1, 1299.00);
```

---

## Task 1 — Orders + who placed them

**Goal:** show every order together with information about the user who placed it.

**Fields:** order_id, order_date, status, user_name, country

```sql
SELECT
    orders.id AS order_id,
    orders.order_date AS order_date,
    orders.status,
    users.name AS user_name,
    users.country
FROM orders
INNER JOIN users ON orders.user_id = users.id;
```

**Notes:**
- Started from `orders`, since the task requires *all orders* — `orders` determines the row count.
- `INNER JOIN` and `LEFT JOIN` return the same result here, because every `orders.user_id` is guaranteed to exist in `users` (enforced by the foreign key). The two only diverge when starting from the table that *doesn't* determine row count, or when unmatched rows exist — neither is the case here.

---

## Task 2 — Contents of a single order

**Goal:** show what was purchased in order 103.

**Fields:** order_id, product_name, quantity, price_at_purchase

```sql
SELECT
    order_items.order_id,
    products.name AS product_name,
    order_items.quantity,
    order_items.price_at_purchase
FROM order_items
JOIN products ON order_items.product_id = products.id
WHERE order_items.order_id = 103;
```

**Notes:**
- Started from `order_items`, since it's the table that actually holds "which product, in which order, at what price" — `orders` and `products` on their own don't have that link.
- Moving `order_id = 103` from `WHERE` into the `ON` clause (`JOIN products ON order_items.product_id = products.id AND order_items.order_id = 103`) gives the same result with an `INNER JOIN`, since unmatched rows are dropped either way. The difference only shows up with a `LEFT JOIN`: filtering in `ON` keeps the unmatched left-side rows (with `NULL`s), while filtering in `WHERE` removes them entirely.

---

## Task 3 — Full receipt across all orders

**Goal:** show every order and every product inside it, like a shop receipt.

**Fields:** order_id, user_name, product_name, quantity, price_at_purchase

```sql
SELECT
    order_items.order_id,
    users.name AS user_name,
    products.name AS product_name,
    order_items.quantity,
    order_items.price_at_purchase
FROM order_items
JOIN orders ON order_items.order_id = orders.id
JOIN users ON orders.user_id = users.id
JOIN products ON order_items.product_id = products.id;
```

**Notes:**
- `order_items` doesn't link to `users` directly — there's no shared column. The connection goes through `orders` (`order_items` → `orders` → `users`), so three joins are needed in total.

---

## Task 4 — All users + their orders (including users with none)

**Goal:** list every user together with their orders, keeping users who have never ordered.

**Fields:** user_name, order_id, status

```sql
SELECT
    users.name AS user_name,
    orders.id AS order_id,
    status
FROM users
LEFT JOIN orders ON orders.user_id = users.id;
```

**Notes:**
- Started from `users` this time, since the task requires *all users* to be preserved. `LEFT JOIN` keeps Charlie (no orders) in the result, with `order_id`/`status` returned as `NULL`. An `INNER JOIN` here would drop him entirely.

---

## Task 5 — Products and where they were purchased

**Goal:** show every product and the orders it appears in, including products that were never purchased.

**Fields:** product_name, order_id, quantity

```sql
SELECT
    products.name AS product_name,
    order_items.order_id AS order_id,
    order_items.quantity AS quantity
FROM products
LEFT JOIN order_items ON products.id = order_items.product_id;
```

**Notes:**
- Same pattern as Task 4, mirrored for products: start from `products` (the table that must be fully preserved), `LEFT JOIN` to `order_items` so an unpurchased product would still appear with `NULL` order fields. In this dataset every product has at least one purchase, so there's no visible "orphan" row — but the query is written to handle one correctly if it existed.

---

## Task 6 — Paid orders only, with contents

**Goal:** show the contents of orders with `status = 'paid'` only.

**Fields:** order_id, user_name, product_name, quantity, price_at_purchase

```sql
SELECT
    order_items.order_id,
    users.name AS user_name,
    products.name AS product_name,
    order_items.quantity,
    order_items.price_at_purchase
FROM order_items
JOIN orders ON order_items.order_id = orders.id
JOIN users ON orders.user_id = users.id
JOIN products ON order_items.product_id = products.id
WHERE orders.status = 'paid';
```

**Notes:**
- With a plain `INNER JOIN`, filtering `status = 'paid'` in `WHERE` or moving it into the `orders` join's `ON` clause produces the same result — `INNER JOIN` discards unmatched rows regardless of where the extra condition sits. The distinction only matters with a `LEFT JOIN`: putting the status filter in `ON` would keep users/orders without a paid order (as `NULL` rows), while `WHERE` would remove them from the result entirely.

---

## Task 7 — Everything Alice bought

**Goal:** show every product Alice purchased, across all of her orders.

**Fields:** order_id, order_date, product_name, quantity, price_at_purchase

```sql
SELECT
    order_items.order_id,
    orders.order_date,
    products.name AS product_name,
    order_items.quantity,
    order_items.price_at_purchase
FROM order_items
JOIN orders ON order_items.order_id = orders.id
JOIN users ON orders.user_id = users.id
JOIN products ON order_items.product_id = products.id
WHERE users.name = 'Alice';
```

**Notes:**
- If Alice had no orders at all, this query would return **0 rows**, not an error. With an `INNER JOIN`, a user without a matching order is dropped at the first join already, so there's nothing left for `WHERE` to filter.

---

## Task 8 — Orders with no items (data integrity check)

**Goal:** find orders that exist but have no rows in `order_items` (an order created but never filled in).

**Fields:** order_id, user_id, status

```sql
SELECT
    orders.id AS order_id,
    orders.user_id AS user_id,
    orders.status AS status
FROM orders
LEFT JOIN order_items ON orders.id = order_items.order_id
WHERE order_items.order_id IS NULL;
```

**Notes:**
- Started from `orders` (the table whose rows must all be considered), `LEFT JOIN` to `order_items` so unmatched orders survive the join with `NULL` in the `order_items` columns, then `WHERE ... IS NULL` isolates exactly those unmatched rows.
- Replacing `LEFT JOIN` with `INNER JOIN` here would always return 0 rows, regardless of whether an itemless order actually exists — `INNER JOIN` drops unmatched orders before `WHERE` ever runs, so there's nothing left to check for `NULL`. This is the core idea the task is testing: `LEFT JOIN` + `IS NULL` is the standard pattern for finding "orphan" rows; `INNER JOIN` breaks that pattern entirely.
- No orders are currently itemless in this dataset, so the query correctly returns 0 rows.
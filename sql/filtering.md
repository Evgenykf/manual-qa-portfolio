# SQL — Filtering

Practice exercises covering `WHERE`, `BETWEEN`, `IN`, combined `AND`/`OR` conditions (with parentheses to control evaluation order), `LIKE`/`ILIKE`, and computed columns with `AS` — based on a single `products` table.

## Schema

```sql
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    category VARCHAR(100),
    price DECIMAL(10,2),
    in_stock BOOLEAN
);

INSERT INTO products (id, name, category, price, in_stock) VALUES
    (1, 'iPhone 15', 'Электроника', 80000, TRUE),
    (5, 'Наушники Pro', 'Электроника', 12000, TRUE),
    (8, 'MX Master Mouse', 'mouse', 1200, TRUE),
    (9, 'Budget Tablet', 'tablet', 900, TRUE),
    (10, 'Android Smartphone', 'smartphone', 1300, TRUE),
    (11, 'Smartwatch Basic', 'smartwatch', 700, TRUE),
    (12, 'USB-C Cable', 'accessory', 80, TRUE),
    (13, 'Old Camera', 'camera', 500, FALSE),
    (14, 'Never Ordered Item', 'accessory', 600, TRUE);
```

---

## Task 1 — Products in stock

**Goal:** return the products that can be shown to a customer.

**Fields:** id, name, category, price

```sql
SELECT id, name, category, price
FROM products
WHERE in_stock = TRUE
ORDER BY id ASC;
```

**Notes:**
- `in_stock` is a boolean column, so `in_stock = TRUE` (or just `WHERE in_stock`) filters directly on it — no string comparison needed.

---

## Task 2 — Products in a price range

**Goal:** find products within an easy-to-understand price range.

**Fields:** name, category, price

```sql
SELECT name, category, price
FROM products
WHERE price BETWEEN 500 AND 900
ORDER BY price ASC;
```

**Notes:**
- `BETWEEN` is inclusive on both ends — 500 and 900 themselves are included, unlike writing `price > 500 AND price < 900`.

---

## Task 3 — Categories for an ad campaign

**Goal:** keep only products from the categories a marketer wants to advertise.

**Fields:** id, name, category, price

```sql
SELECT id, name, category, price
FROM products
WHERE category IN ('tablet', 'smartphone', 'mouse')
  AND price >= 100
ORDER BY price DESC;
```

**Notes:**
- `IN (...)` checks membership in a list — cleaner than chaining several `category = '...' OR category = '...'` conditions.
- `>=` was needed specifically because the requirement was "price not less than 100" (100 itself included), not "price greater than 100".

---

## Task 4 — Discount basket

**Goal:** bundle products for a promotion: very cheap or very expensive items, in stock only.

**Fields:** name, price, price_with_discount (15% off)

```sql
SELECT name, price, price * 0.85 AS price_with_discount
FROM products
WHERE (price < 100 OR price > 50000)
  AND in_stock;
```

**Notes:**
- The parentheses around `price < 100 OR price > 50000` are required. Without them, `AND` binds tighter than `OR` in SQL, so `WHERE price < 100 OR price > 50000 AND in_stock` would actually evaluate as `price < 100 OR (price > 50000 AND in_stock)` — silently including out-of-stock cheap items, which isn't the intended logic.
- A 15% discount means the customer pays 85% of the price, hence `* 0.85`.

---

## Task 5 — Search by product name

**Goal:** simulate a site search box: match a term inside the name, case-insensitively, and separately exclude a term.

**Fields:** name

```sql
-- products with "pro" in the name (case-insensitive)
SELECT name FROM products
WHERE name ILIKE '%pro%';

-- products with "mx" in the name (case-insensitive)
SELECT name FROM products
WHERE name ILIKE '%mx%';

-- products that do NOT have "iPhone" in the name
SELECT name FROM products
WHERE name NOT ILIKE '%iPhone%';
```

**Notes:**
- `ILIKE` (unlike `LIKE`) ignores case, matching the "regardless of case" requirement.
- `%` on both sides of the search term means "match anywhere inside the string", not just an exact match.

---

## Task 6 — Products ready for the storefront

**Goal:** configure the main storefront: only products that are actually sellable right now.

**Fields:** name, category, price, price_with_markup (30% markup), price_with_vat (price increased by 50%)

```sql
SELECT
    name,
    category,
    price,
    price * 1.30 AS price_with_markup,
    price * 1.50 AS price_with_vat
FROM products
WHERE in_stock = TRUE
  AND price BETWEEN 100 AND 1300
  AND category != 'smartwatch';
```

**Notes:**
- `category != 'smartwatch'` is a direct equality check, not a pattern match — `NOT ILIKE '%smartwatch%'` would have been the wrong tool here, since the category column holds an exact value, not free text to search inside.
- Combines everything from the earlier tasks in one query: a boolean filter, an inclusive range, an exclusion, and two independent computed columns.
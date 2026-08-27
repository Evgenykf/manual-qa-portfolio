# SQL — Basics: Table Design (DDL)

Practice exercise in designing a schema from scratch: choosing appropriate data types and constraints for four related tables (`users`, `products`, `orders`, `order_items`), given a fixed field list and a short set of design rules.

## Requirements

- `users.id`, `products.id`, `orders.id` — primary keys
- `order_items` has a composite key: `order_id` + `product_id`
- `orders.user_id` must match the type of `users.id`
- `order_items.order_id` must match the type of `orders.id`
- `order_items.product_id` must match the type of `products.id`
- `created_at` and `order_date` must store both date and time
- `price` and `price_at_purchase` must be usable in calculations

## Schema

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age SMALLINT,
    email VARCHAR(100),
    country VARCHAR(2),
    is_active BOOLEAN,
    created_at TIMESTAMPTZ NOT NULL
);

CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    category VARCHAR(50),
    price DECIMAL(10,2) NOT NULL,
    in_stock BOOLEAN
);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT NOT NULL,
    order_date TIMESTAMPTZ NOT NULL,
    status VARCHAR(20),
    FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity SMALLINT NOT NULL,
    price_at_purchase DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);
```

## Type choices and reasoning

- **`age SMALLINT`, not `INT`.** Age is always a small number (nobody is going to be 50,000 years old), so `SMALLINT` is enough. Using `INT` here would just waste space for no reason.
- **`country VARCHAR(2)`, not a longer text field.** Instead of letting people type the country name however they want (`"USA"`, `"United States"`, `"usa"`), I used a fixed 2-letter code like `RU` or `US`. This keeps the data consistent and easier to filter later.
- **`created_at` / `order_date` as `TIMESTAMPTZ`, not `DATE`.** The task said these need to store both a date and a time, so plain `DATE` wasn't enough. I picked `TIMESTAMPTZ` over a regular `TIMESTAMP` because it also keeps track of the time zone — useful since users could be registering from different countries.
- **`price` / `price_at_purchase` as `DECIMAL(10,2)`, not `INT` in cents or `FLOAT`.** `FLOAT` can round money values in weird ways, which is risky for prices. `DECIMAL` keeps the exact number and is still easy to read as a normal price (like `999.00`), without needing to convert from cents every time.
- **`status VARCHAR(20)`, not a number code.** I could have used `1`, `2`, `3` for order statuses, but then I'd have to remember what each number means. Writing `'paid'` or `'cancelled'` directly is clearer and easier to check while testing or debugging.
- **`quantity SMALLINT`, not `INT`.** Nobody orders millions of the same item in one order line, so a small number type is enough here too.
- **Matching foreign key types.** `orders.user_id`, `order_items.order_id`, and `order_items.product_id` are all `INT`, same as the `id` columns they point to (`users.id`, `orders.id`, `products.id`). A foreign key has to be the same type as the column it's linked to, otherwise the link wouldn't make sense.
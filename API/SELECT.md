# SELECT

## 1. Overview

The `select()` method creates a SELECT query.

It allows developers to specify which columns should be retrieved from a database table.

SimpleSql-TS follows SQL terminology and behavior. The generated SQL must remain predictable and easy to understand.

## 2. Basic Usage

Select specific columns:

```typescript
const products = await db
  .select("id", "title", "price")
  .from("products")
  .exec();
```

Generated SQL:

```sql
SELECT id, title, price
FROM products
```

Select all columns:

```typescript
const products = await db
  .select()
  .from("products")
  .exec();
```

Generated SQL:

```sql
SELECT *
FROM products
```

Calling `select()` without arguments is equivalent to selecting all columns.

## 3. Method Signature

```typescript
select(...columns: string[]): SelectQueryBuilder;
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| columns | string[] | Column names to retrieve |

If no columns are provided, the compiler must generate `SELECT *`.

Column names may include table qualifiers.

Example:

```typescript
db.select(
  "products.id",
  "products.title",
  "categories.name"
);
```

## 4. Table Selection

The `from()` method specifies the source table.

```typescript
const products = await db
  .select("id", "title")
  .from("products")
  .exec();
```

The `from()` method is part of the SELECT query API and is documented separately.

## 5. Column Aliases

Column aliases should follow SQL syntax.

Example:

```typescript
const users = await db
  .select(
    "id AS user_id",
    "name AS username"
  )
  .from("users")
  .exec();
```

Generated SQL:

```sql
SELECT id AS user_id, name AS username
FROM users
```

## 6. SQL Expressions

SQL expressions and database-specific functions can be included using the `raw()` expression.

Example:

```typescript
const products = await db
  .select(
    "id",
    "title",
    db.raw("LOWER(title) AS normalized_title")
  )
  .from("products")
  .exec();
```

Raw expressions must support parameter bindings when values are required.

SQL expressions must not be confused with ordinary column identifiers.

## 7. Query Composition

SELECT queries can be combined with other query clauses.

Example:

```typescript
const products = await db
  .select("id", "title", "price")
  .from("products")
  .where("status", "=", "active")
  .orderBy("created_at", "DESC")
  .limit(20)
  .exec();
```

Each clause has its own API specification.

See:

- [FROM](FROM.md)
- [WHERE](WHERE.md)
- [JOIN](JOIN.md)
- [ORDER_BY](ORDER_BY.md)
- [LIMIT](LIMIT.md)

## 8. SQL Compilation

The `toSql()` method returns the generated SQL statement and its parameter bindings without executing the query.

Example:

```typescript
const query = db
  .select("id", "title")
  .from("products")
  .where("status", "=", "active");

const [sql, bindings] = query.toSql();
```

Expected result:

```typescript
[
  "SELECT id, title FROM products WHERE status = $1",
  ["active"]
]
```

The generated SQL syntax and parameter placeholders are determined by the configured database compiler.

## 9. Query Execution

The `exec()` method executes the SELECT statement and returns the resulting rows.

Example:

```typescript
const products = await db
  .select("id", "title", "price")
  .from("products")
  .where("status", "=", "active")
  .exec();
```

The result contains the rows returned by the database driver.

SELECT queries must not execute automatically during query construction.

## 10. Design Rules

The SELECT API must follow these rules:

- Calling `select()` without arguments selects all columns.
- Column selection must remain explicit and predictable.
- Column identifiers must be handled separately from SQL expressions.
- SQL expressions must use explicit raw expressions.
- Values must use parameter bindings.
- Query construction must not execute database operations.
- SQL compilation must be independent of database execution.
- The SELECT API must not introduce ORM behavior.

## 11. Scope

The initial SELECT implementation focuses on:

- Selecting specific columns.
- Selecting all columns.
- Qualified column names.
- Column aliases.
- Raw SQL expressions.
- Query composition with other clauses.
- SQL compilation.
- Query execution.

Additional SQL features should be introduced only when they provide clear value without compromising the simplicity of the API.

# Design Principles

## 1. Purpose

SimpleSql-TS is designed to provide a lightweight and predictable way to build SQL queries in TypeScript.

Its purpose is not to abstract SQL or replace the developer's knowledge of relational databases.

The library should make SQL query construction easier while preserving the developer's control over the statements being generated and executed.

## 2. SQL First

SQL is the foundation of SimpleSql-TS.

The public API should follow SQL concepts and terminology as closely as possible.

Methods should represent recognizable SQL operations rather than introduce a proprietary query language.

The developer should be able to understand the generated SQL by looking at the TypeScript code.

**Rule:** Never introduce an abstraction that makes the resulting SQL difficult to predict.

## 3. Predictability

Generated SQL must be deterministic and understandable.

The same query definition, compiler configuration, and bindings must produce the same SQL structure.

The library must not:

- Generate unexpected queries.
- Execute additional queries automatically.
- Load relationships implicitly.
- Modify query definitions silently.
- Introduce hidden database operations.

Every database operation must be explicitly requested by the developer.

## 4. No ORM Behavior

SimpleSql-TS is not an ORM.

It must not introduce:

- Entity or model classes.
- Active Record patterns.
- Automatic relationship management.
- Lazy loading.
- Automatic object persistence.
- Implicit schema management.
- Automatic query generation based on object relationships.

Database tables and SQL statements remain the developer's responsibility.

## 5. Minimal API

The public API should remain small and focused.

Each method must have a clear purpose.

Avoid:

- Multiple methods that perform essentially the same operation.
- Excessive configuration options.
- Redundant aliases.
- Unnecessary helper classes.
- Complex abstractions for simple SQL operations.

New methods should only be introduced when they provide meaningful value.

## 6. Fluent Interface

The Query Builder should provide a readable fluent API.

Method names should be consistent with SQL terminology.

For example:

```typescript
db.select("id", "title")
  .from("products")
  .where("status", "=", "active")
  .orderBy("created_at", "DESC")
  .limit(20);
```

The fluent interface should improve readability without hiding query structure.

## 7. Explicit Execution

Building a query must not execute it.

Execution must happen only when the developer explicitly requests it.

The library should provide separate methods for:

- Building queries.
- Inspecting generated SQL.
- Executing queries.

The `toSql()` method must never execute a database operation.

The `exec()` method is responsible for executing the query.

## 8. Parameter Binding

All user-provided values must be handled through parameter bindings.

The library must never interpolate values directly into SQL statements.

This applies to:

- SELECT conditions.
- INSERT values.
- UPDATE values.
- JOIN conditions.
- HAVING conditions.
- Raw SQL expressions.

SQL identifiers, such as table names and column names, must be handled separately from

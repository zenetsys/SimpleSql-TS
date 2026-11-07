# SimpleSql-TS

**A lightweight SQL query builder for TypeScript.**

SimpleSql-TS is a lightweight library for building and executing SQL queries using a fluent TypeScript API.

Its goal is simple: bring SQL into TypeScript without hiding its structure or introducing the complexity of an ORM.

## Philosophy

SimpleSql-TS is built around a few fundamental principles:

- **SQL predictability:** Generated SQL should be clear, explicit, and predictable.
- **No ORM abstraction:** The library builds SQL queries; it does not attempt to map databases into objects or entities.
- **Simplicity:** A small API with focused responsibilities.
- **TypeScript support:** Take advantage of TypeScript's type system without unnecessary complexity.
- **Parameter binding:** Use parameterized queries to handle values safely.
- **Driver independence:** Separate SQL generation from query execution.

The developer should always understand the SQL being generated and executed.

## Features

- Fluent SQL query builder.
- SELECT, INSERT, UPDATE, and DELETE statements.
- WHERE, JOIN, GROUP BY, HAVING, ORDER BY, and LIMIT clauses.
- Parameterized queries.
- Raw SQL expressions for database-specific operations.
- JSON and JSONB value support.
- SQL generation without execution.
- Transaction support.
- PostgreSQL as the initial database engine.
- Extensible architecture for additional database engines.

## Database Support

PostgreSQL is the first supported database engine.

The architecture is designed to allow additional SQL database engines to be introduced without coupling query construction to a specific database implementation.

## SQL Generation and Execution

SimpleSql-TS separates query construction, SQL compilation, and database execution.

Queries can be compiled independently to inspect the generated SQL and its parameter bindings before execution.

```typescript
const [sql, bindings] = query.toSql();
```

This provides transparency and allows developers to retain control over their database interactions.

## What SimpleSql-TS Is Not

SimpleSql-TS is not an ORM.

It does not aim to provide:

- Automatic object-relational mapping.
- Entity or model management.
- Implicit relationship loading.
- Hidden query generation.
- Database abstraction that obscures SQL behavior.

It is a tool for developers who understand SQL and want a more convenient way to build queries in TypeScript.

## Documentation

- [Architecture](ARCHITECTURE.md)
- [Design Principles](DESIGN_PRINCIPLES.md)
- [Public API](API/)

## Project Status

Early development.

The initial development focuses on PostgreSQL, the core query builder, SQL compilation, and database execution.

## License

To be defined.


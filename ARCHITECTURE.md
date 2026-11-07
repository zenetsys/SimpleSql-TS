# Architecture

## 1. Overview

SimpleSql-TS is a lightweight SQL query builder for TypeScript.

Its architecture separates query construction, SQL compilation, and database execution.

The library is designed around three fundamental responsibilities:

1. Build and maintain SQL query definitions.
2. Compile query definitions into SQL statements and parameter bindings.
3. Execute compiled queries through a database driver.

PostgreSQL is the initial supported database engine. The architecture should allow additional database engines to be introduced without changing the public query-building API.

## 2. Design Principles

The architecture follows these principles:

- **Separation of concerns:** Query construction, compilation, and execution have independent responsibilities.
- **SQL predictability:** The generated SQL must be understandable and predictable.
- **Driver independence:** Query construction must not depend on a specific database driver.
- **Compiler independence:** Database-specific SQL syntax belongs to the corresponding compiler.
- **Minimal abstraction:** Components should exist only when they provide a clear responsibility.
- **TypeScript support:** Public APIs should take advantage of TypeScript's type system without introducing unnecessary complexity.
- **Parameterized queries:** Values must be passed through parameter bindings rather than interpolated into SQL strings.
- **Extensibility:** New database engines and drivers should be possible without redesigning the core.

## 3. Architecture Layers

SimpleSql-TS is organized into four logical layers.

### 3.1. Query Builder

Responsible for providing the public fluent API.

It allows developers to construct SQL queries using methods such as:

- select()
- insert()
- update()
- delete()
- from()
- where()
- join()
- orderBy()
- groupBy()
- having()
- limit()

The Query Builder must not generate database-specific SQL or execute database operations directly.

Its responsibility is to build and maintain a query definition.

### 3.2. Query Definition

Represents the internal structure of a query.

It stores the information required to compile a SQL statement, including:

- Query type.
- Selected columns.
- Source tables.
- Conditions.
- Joins.
- Groups.
- Ordering.
- Limits and offsets.
- Insert and update values.
- Raw SQL expressions.

The Query Definition must be independent of the database engine.

It acts as the intermediate representation between the public API and the SQL compiler.

### 3.3. SQL Compiler

Responsible for translating a Query Definition into executable SQL.

The compiler handles:

- SQL syntax.
- Identifier quoting.
- Parameter placeholders.
- Query clause ordering.
- Database-specific expressions.
- SQL generation.
- Compilation validation.

Each database engine has its own compiler implementation.

Initial implementation:

- PostgreSqlCompiler

Future implementations may include:

- MySqlCompiler
- SQLiteCompiler
- SqlServerCompiler

The compiler must not establish database connections or execute SQL statements.

Its output consists of:

- SQL statement.
- Parameter bindings.

### 3.4. Database Execution

Responsible for executing compiled SQL through a database driver.

The execution layer handles:

- Query execution.
- Result retrieval.
- Connection management.
- Transactions.
- Driver-specific result handling.
- Database errors.

The execution layer must not generate SQL or modify query definitions.

Drivers provide the interface between SimpleSql-TS and the underlying database client.

## 4. Component Responsibilities

### Database

The main entry point for database operations.

Responsibilities:

- Configure the database compiler.
- Configure the database driver.
- Create query builders.
- Coordinate query compilation and execution.
- Expose transaction support.

Database must not contain database-specific SQL generation logic.

### CompilerResolver

Responsible for resolving the appropriate SQL compiler based on the configured database engine.

The public API should identify the engine by name rather than requiring users to instantiate compiler implementations directly.

### QueryBuilder

Provides the fluent API used to construct queries.

It maintains the query definition and exposes compilation and execution methods.

### QueryDefinition

Stores the complete internal representation of a query.

It must remain independent of database-specific syntax.

### SqlCompiler

Defines the common compiler contract.

It establishes the operations required to compile supported query types.

### PostgreSqlCompiler

Implements PostgreSQL-specific SQL generation.

It handles PostgreSQL syntax, identifier quoting, parameter placeholders, JSONB-related expressions when represented as SQL expressions, and other engine-specific behavior.

### Driver

Defines the contract required to execute SQL statements.

A driver implementation adapts an external database client to the SimpleSql-TS execution interface.

The library should not require a specific driver implementation.

### Raw Expression

Represents an explicit SQL expression supplied by the developer.

Raw expressions allow database-specific SQL functionality without requiring every SQL feature to have a dedicated Query Builder method.

Raw expressions must support parameter bindings.

## 5. Query Lifecycle

A query follows this execution flow:

Application → Database → QueryBuilder → QueryDefinition → SqlCompiler → SQL + Bindings → Driver → Database Result

The lifecycle has the following stages:

1. The application requests a query builder from Database.
2. QueryBuilder receives the fluent API calls.
3. QueryBuilder updates the QueryDefinition.
4. The compiler validates and compiles the definition.
5. The compiler produces a SQL statement and its bindings.
6. The driver executes the compiled statement.
7. The execution layer returns the result to the application.

### SQL Inspection

Query compilation must be available independently of execution.

The `toSql()` method returns the generated SQL and its bindings without executing the query.

This allows developers to inspect, debug, and test generated SQL.

## 6. Parameter Bindings

Parameter bindings are a fundamental part of the architecture.

Values must never be interpolated directly into generated SQL.

The compiler is responsible for generating the appropriate parameter placeholders for the selected database engine.

The driver receives the SQL statement and its bindings separately.

The binding system must support:

- Strings.
- Numbers.
- Booleans.
- Null values.
- Dates.
- JSON-compatible values.
- Other values supported by the underlying driver.

JSON and JSONB values must be handled through parameter bindings.

The library should not introduce a separate JSON query abstraction in its initial implementation.

Database-specific JSON operations can be expressed through Raw Expressions.

## 7. Transactions

Transaction management belongs to the database execution layer.

The architecture must support:

- Beginning transactions.
- Committing transactions.
- Rolling back transactions.
- Executing multiple queries within a transaction.

Queries executed inside a transaction must use the transaction's connection or execution context.

Transaction handling must be independent of the query compiler.

## 8. Database Engine Extensibility

The architecture separates database-specific behavior from the core query-building functionality.

Adding a new database engine should primarily require:

1. Implementing the corresponding SQL compiler.
2. Defining the engine-specific syntax and parameter placeholders.
3. Providing or integrating a compatible database driver.

The Query Builder and Query Definition should remain unchanged whenever possible.

PostgreSQL is the initial implementation and serves as the reference for the compiler architecture.

## 9. Error Handling

Errors should be separated according to their origin.

The architecture distinguishes between:

- Query definition errors.
- SQL compilation errors.
- Driver execution errors.
- Database errors.
- Transaction errors.

The library should preserve the original driver or database error whenever possible.

It should not silently replace useful database error information with generic exceptions.

## 10. Initial Project Structure

The initial source structure is organized by responsibility.

```text
src/
├── query/
│   ├── QueryBuilder.ts
│   └── QueryDefinition.ts
│
├── compiler/
│   ├── SqlCompiler.ts
│   └── PostgreSqlCompiler.ts
│
├── database/
│   ├── Database.ts
│   ├── CompilerResolver.ts
│   └── Driver.ts
│
├── expressions/
│   └── Raw.ts
│
└── mod.ts
```

Additional components should be introduced only when their responsibilities justify their existence.

The project should avoid unnecessary directories and abstractions during initial development.

## 11. Out of Scope

SimpleSql-TS is a SQL query builder, not an ORM.

The following features are outside its architectural scope:

- Object-relational mapping.
- Entity and model management.
- Automatic relationship loading.
- Automatic schema synchronization.
- Active Record patterns.
- Hidden query generation.
- Automatic database migrations.

Database schema management and migrations can be handled by external tools.

## 12. Initial Development Scope

The initial implementation focuses on PostgreSQL.

Development should follow this order:

1. Core query definitions.
2. Query Builder API.
3. PostgreSQL SQL compiler.
4. Parameter binding.
5. Database driver interface.
6. PostgreSQL driver integration.
7. Query execution.
8. Transactions.
9. Testing and documentation.

The initial implementation should prioritize correctness, predictable SQL generation, and a stable public API over feature quantity.

## 13. Architectural Goal

SimpleSql-TS aims to provide a straightforward way to work with SQL in TypeScript.

The architecture must preserve the developer's control over SQL while reducing the amount of manual query construction.

**Build SQL. Keep it predictable.**

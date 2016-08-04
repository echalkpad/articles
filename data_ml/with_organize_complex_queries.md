# with -- Organize Complex Queries

[Original URL](https://modern-sql.com/feature/with)

> In imperative programming it is common practice to group instructions in small and easily comprehensible units--namely functions or methods. Often to enable code reuse but also to improve...

In imperative programming it is common practice to group instructions in small and easily comprehensible units--namely functions or methods. Often to enable code reuse but also to improve readability.

Even though SQL has functions and procedures too, they are not the primary tool to make complex SQL queries comprehensible or to enable code reuse. In SQL neither functions nor procedures are [first-class citizens](https://en.wikipedia.org/wiki/First-class_citizen) they way sub-queries are[0](https://modern-sql.com/feature/with#footnote-0).

In SQL the building block of programs are queries--not instructions. Consequently, SQL has views to store queries natively so they can be reused.

In addition, SQL:1999 introduced the `with` clause to define "views" within queries. These `with`-queries are only visible in a single statement and can thus be used to improve the structure of this statement without polluting the global namespace.

The `with` feature is also known as _common table expression (CTE)_ and _sub-query factoring_. Note that the `recursive` from of the `with` clause is covered in [another article](https://modern-sql.com/coming-up#feature-with-recursive).

## Syntax

`with` is a prefix to the `select` keyword[1](https://modern-sql.com/feature/with#footnote-1):

```
WITH query_name (column_name1, ...) AS
 (SELECT ...)

SELECT ...
```

The syntax following `with` is the same as for `create view`: it starts with the _query name_ and optionally[2](https://modern-sql.com/feature/with#footnote-2) in parenthesis the name of the columns it returns. The `as` keyword finally introduces the definition itself (the query)--again in parentheses.

`With` is not a stand alone command like `create view` is: it must be followed by `select`. This query (and sub-queries it contains) can refer to the just defined query name in their `from` clause.

A single `with` clause can introduce multiple query names by separating them with a comma (the `with` keyword itself is not repeated). Each of these queries can refer to the query names previously defined within the same `with` clause[3](https://modern-sql.com/feature/with#footnote-3) (but see [Compatibility](https://modern-sql.com/feature/with#feature-with-compatibility)):

```
WITH query_name1 AS (
 SELECT ...
 )
 , query_name2 AS (
 SELECT ...
 FROM query_name1
 ...
 )
SELECT ...
```

Query names defined using `with` mask existing tables or views with the same name[4](https://modern-sql.com/feature/with#footnote-4).

Normally, databases handle `with`-queries like they handle views: they substitute the reference to the query by its definition and then optimize and execute the overall query.

The PostgreSQL database is different in this regard (as of 9.5[5](https://modern-sql.com/feature/with#footnote-5)): it treads the `with` clause as [optimization fence](http://blog.2ndquadrant.com/postgresql-ctes-are-optimization-fences/). The queries inside the `with` clause are optimized (planned) in isolation. This can have a significant performance impact as compared to sub-queries directly written into the `from` clause.

The SQL standard does not define how to optimize querys--both approaches are conforming.

## Use Cases

## Compatibility

The `with` clause was introduced with [SQL:1999](https://modern-sql.com/standard) as a set of [optional features](https://modern-sql.com/standard/levels). Leaving the `recursive` variant aside, the features are T121 for top-level `with` clauses and T122 for `with` clauses in sub-queries.

The basic functionality of `with` is well-supported. The single area where products behave differently is name resolution. It is especially notable that `with` is often treated like [`with recursive`](https://modern-sql.com/coming-up#feature-with-recursive)[6](https://modern-sql.com/feature/with#footnote-6). The more subtle incompatibilities are related to qualified table names (`schema.table` cannot refer to a `with` query[7](https://modern-sql.com/feature/with#footnote-7)) and views used in the scope of `with`-queries (the query inside the view does not "see" the outer `with` clause[8](https://modern-sql.com/feature/with#footnote-8)).

Some use cases can be implemented using views. However, this can quickly lead to an unreasonable number of views ("namespace pollution"). In those cases sub-queries are often the better option.

## Proprietary Extensions

### PostgreSQL: Writeable CTEs

Starting with 9.1 the PostgreSQL database supports DML statements (`insert`, `update`, `delete`) within the body of `with`-queries ([docs](http://www.postgresql.org/docs/9.5/static/queries-with.html#QUERIES-WITH-MODIFYING)). When using the (also proprietary) `returning` clause of the DML statement, the `with`-query actually returns data (e.g, the just inserted rows).

### Oracle: Functions in `with`

The Oracle Database support function and procedure declarations within the `with` clause since version 12cR1 ([docs](http://docs.oracle.com/database/121/SQLRF/statements_10002.htm#i2066378)).

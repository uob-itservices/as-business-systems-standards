# General PL/SQL Writing Guidance

This page contains some general guidance on how to approach writing pl/sql, but it is not intended to be an exhaustive list of good practice.


* [Column Aliases](#column-aliases)
* [SQL Table Joins](#table-joins)
* [Transaction Control - COMMIT and ROLLBACK](#transaction-control-commit-and-rollback)
* [Single Responsibility Principle](#single-responsibility-principle)
* [Conditional Statements](#conditional-statements)


## Column Aliases

Always alias aggregates and function-wrapped columns:

```sql
SELECT
    name,
    SUM(amount) AS sum_amount
FROM ...
```

Always alias all columns when selecting with table aliases:

```sql
SELECT
    projects.name AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.backings AS backings
    INNER JOIN ksr.projects AS projects ON ...
```

Always use `AS` to alias columns:

__GOOD__:

```sql
SELECT
    projects.name AS project_name,
    COUNT(backings.id) AS backings_count
...
```

__BAD__:

```sql
SELECT
    projects.name project_name,
    COUNT(backings.id) backings_count
...
```

Order (`ASC`, `DESC`) should always be explicit. All window functions should be aliased.

---

## SQL Table Joins

__USE ANSI JOIN SYNTAX!__

Explicitly use `INNER JOIN` not just `JOIN`, making multiple lines of `INNER JOIN`s easier to scan:

__GOOD__:

```sql
SELECT
    projects.name      AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.projects AS projects
    INNER JOIN ksr.backings AS backings
    ON ...
    LEFT OUTER JOIN ksr.backer_rewards AS backer_rewards 
    ON ...
    LEFT OUTER JOIN ...
```

__BAD__:

```sql
SELECT
    projects.name      AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.projects AS projects
    JOIN ksr.backings AS backings 
    ON ...
    LEFT OUTER JOIN ksr.backer_rewards AS backer_rewards
    ON ...
    LEFT OUTER JOIN ...
```

---

## Transaction Control COMMIT and ROLLBACK 

A function or procedure should take a parameter to specify this behavior. This means it can be controlled by the calling code and makes it more flexible.

In your package spec specify constants for commit and rollback:

```sql
g_COMMIT   CONSTANT NUMBER := 1;
g_ROLLBACK CONSTANT NUMBER := -1;
```

Pass this value as a parameter into functions and procedures:

```sql
PROCEDURE get_transactions(i_commit IN NUMBER)
```

It can then be used like this:

```sql
IF p_commit = g_COMMIT THEN
    DSP.line('package.get_transactions', 'Commit');
    COMMIT;
ELSIF p_commit = g_ROLLBACK THEN
    DSP.line('package.get_transactions', 'Rollback');
    ROLLBACK;
END IF;
```

## Single Responsibility Principle

When writing code always consider the [Single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle). Each block of code should do one thing, and one thing well.


## Conditional Statements

Unless your use case absolutely requires a `DECODE`, always use a `CASE` statement for conditional processing. 

In all but the simplest scenarios a `CASE` satatement is more readable ( and therefore maintainable ) and handles a larger variety of conditions.

For more information on using `CASE` statements, please see [The Difference Between DECODE and CASE](https://www.oratable.com/decode-case-differences/)

__GOOD__:

```sql
dept_desc := 
    CASE deptno
        WHEN 10 THEN 'Accounting'
        WHEN 20 THEN 'Research'
        ELSE 'Unknown'
    END;
```

__BAD__:

```sql
IF (deptno = 10) THEN
    dept_desc := 'Accounting';
ELSIF (deptno = 20) THEN
    dept_desc := 'Research';
ELSE
    dept_desc := 'Unknown';
END IF;
```


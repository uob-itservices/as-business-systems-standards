# General PL/SQL Writing Guidance

This page contains some general guidance on how to approach writing pl/sql, but it is not intended to be an exhaustive list of good practice.


* [Column Aliases](#column-aliases)
* [ANSI SQL Table Joins](#ansi-sql-table-joins)
* [Single Responsibility Principle](#single-responsibility-principle)
* [Conditional Statements](#conditional-statements)
* [Specifying Parameters in Function and Procedure Calls](#specifying-parameters-in-function-and-procedure-calls)


## Column Aliases

Always alias aggregates and function-wrapped columns:

```sql
    SELECT
        name,
        SUM(amount) AS sum_amount
    FROM...
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

## ANSI SQL Table Joins

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
            WHEN 10 THEN
                'Accounting'
            WHEN 20 THEN
                'Research'
            ELSE 'Unknown'
        END;
```

__BAD__:

```sql
    IF ( deptno = 10 ) THEN
        dept_desc := 'Accounting';
    ELSIF ( deptno = 20 ) THEN
        dept_desc := 'Research';
    ELSE
        dept_desc := 'Unknown';
    END IF;
```

## Specifying Parameters in Function and Procedure Calls

When calling procedures and functions Oracle allows you to declare parameters using the following type of notation:

* Positional notation: You specify the same parameters in the same order as they are declared in the procedure. This notation is compact, but you must specify the parameters (especially literals) in the correct order.
* Named notation: You specify the name of each parameter and its value. An arrow (=>) serves as the association operator. The order of the parameters is not significant.
* Mixed notation: You specify the first parameters with positional notation, then switch to named notation for the last parameters.

You **must always** use **Named notation** and the => associative operator. This protects your code against future alterations/additions to the parameter order and makes your code more readable.

__Example Procedure__

```sql
    ----------------------------------------------------------------------------
    -- SSDT-533: Calculate student profiling result for UNITS based calculation
    PROCEDURE pw_combined_units_profiling_calculation (
        p_borderline      OUT VARCHAR2,
        p_eligible        OUT VARCHAR2,
        p_new_deg_class   OUT swbdegc.swbdegc_recommended_class%TYPE,
        p_new_honr_code   OUT swbense.swbense_honr_code%TYPE)
    ----------------------------------------------------------------------------
```

__GOOD__:

_Named notation_
```sql
        pw_combined_units_profiling_calculation (
            p_borderline      => v_borderline_units,
            p_eligible        => v_eligible_units,
            p_new_deg_class   => v_deg_class_units,
            p_new_honr_code   => v_honr_code_units);
```

__BAD__:

_Positional notation_
```
        pw_combined_units_profiling_calculation (
            v_borderline_units,
            v_eligible_units,
            v_deg_class_units,
            v_honr_code_units);
```

_Mixed notation_
```
        pw_combined_units_profiling_calculation (
            v_borderline_units,
            p_new_honr_code   => v_honr_code_units,
            p_eligible        => v_eligible_units,
            p_new_deg_class   => v_deg_class_units);
```

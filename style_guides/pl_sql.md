# PL/SQL Style guide

This guide was originally stolen from the [Kickstarter SQL Style Guide](https://gist.github.com/fredbenenson/7bb92718e19138c20591)

## Principles

* We take a disciplined and practical approach to writing code.
* We regularly check-in code to Bitbucket
* We believe consistency in style is very important.
* We demonstrate intent explicitly in code, via clear structure and comments where needed.
* We are pragmatic and do not change legacy code for change's sake.

## Rules

### General stuff

* No tabs. 4 spaces per indent.

* No trailing whitespace.

* Unix line endings

* Always capitalize SQL keywords (e.g., `SELECT` or `AS`)

* Variable names should be underscore separated:

  __GOOD__:
  `SELECT COUNT(*) AS backers_count`

  __BAD__:
  `SELECT COUNT(*) AS backersCount`

* Comments should go near the top of your query, or at least near the closest `SELECT`

* Try to only comment on things that aren't obvious about the query (e.g., why a particular ID is hardcoded, etc.)

* Don't use triggers if you don't have to!

* Don't use single letter variable names be as descriptive as possible given the context:

  __GOOD__:
  `SELECT ksr.backings AS backings_with_creators`

  __BAD__:
  `SELECT ksr.backings AS b`
  
---
  
### Naming Conventions

If you are working in an existing package/procedure/function please follow the format that it already uses. For new objects follow these naming conventions.

See [Oracle-Base: Naming Conventions](https://oracle-base.com/articles/misc/naming-conventions) for reference.

* Variable naming:
  ```
  Package Global Variables: g_variable_name
  Local Variables         : l_variable_name
  Types                   : t_type_name
  Cursors                 : c_cursor_name
  Exceptions              : e_exception_name
  Parameters              : p_parameter_name
  ```

* File naming:
  ```
  create_pkw_<name>_spec.sql – Package specification.
  create_pkw_<name>_body.sql – Package body.
  .sql – Everything else.
  ```
  
---

### `SELECT`

Align all columns to the first column on their own line:

```sql
SELECT
    projects.name,
    users.email,
    projects.country,
    COUNT(backings.id) AS backings_count
FROM ...
```

`SELECT` goes on its own line:

```sql
SELECT
    name,
    ...
```

Always rename aggregates and function-wrapped columns:

```sql
SELECT
    name,
    SUM(amount) AS sum_amount
FROM ...
```

Always rename all columns when selecting with table aliases:

```sql
SELECT
    projects.name AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.backings AS backings
    INNER JOIN ksr.projects AS projects ON ...
```

Always use `AS` to rename columns:

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

### `FROM`

Only one table should be in the `FROM`. Never use `FROM`-joins:

__GOOD__:

```sql
SELECT
    projects.name      AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.projects AS projects
    INNER JOIN ksr.backings AS backings 
    ON backings.project_id = projects.id
    ...
```

__BAD__:

```sql
SELECT
    projects.name      AS project_name,
    COUNT(backings.id) AS backings_count
FROM ksr.projects AS projects, ksr.backings AS backings
WHERE
    backings.project_id = projects.id
    ...
```

---

### `JOIN`

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
    LEFT JOIN ksr.backer_rewards AS backer_rewards 
    ON ...
    LEFT JOIN ...
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
    LEFT JOIN ksr.backer_rewards AS backer_rewards
    ON ...
    LEFT JOIN ...
```

Additional filters in the `INNER JOIN` go on new indented lines:

```sql
SELECT
    projects.name      AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.projects AS projects
    INNER JOIN ksr.backings AS backings
    ON projects.id = backings.project_id
    AND backings.project_country != 'US'
    ...
```

The `ON` keyword and condition goes on a  new line:

```sql
SELECT
    projects.name      AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.projects AS projects
    INNER JOIN ksr.backings AS backings 
    ON projects.id = backings.project_id
    ...
```

---

### `WHERE`

Multiple `WHERE` clauses should go on different lines and begin with the SQL operator:

```sql
SELECT
    name,
    goal
FROM 
    ksr.projects AS projects
WHERE
    country = 'US'
    AND TRUNCT(deadline) >= DATE '2015-01-01'
    ...
```

---

### `CASE`

```sql
CASE 
    WHEN category = 'Art' THEN backer_id
    ELSE NULL
END
```

---

### `COMMIT`, `ROLLBACK`

A function or procedure should take a parameter to specify this behavior. This means it can be controlled by the calling code and makes it more flexible.

In your package specify constantans for commit and rollback:

```sql
g_COMMIT   CONSTANT NUMBER := 1;
g_ROLLBACK CONSTANT NUMBER := -1;
```

Pass this value as a paramiter to functions and procedures:

```sql
PROCEDURE get_transactions(p_commit IN NUMBER)
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

## Practices

### Commented code

Delete commented code. If you are worried about losing it, it should be stored in version contol. Leaving commented out code in your source leads to code rot.

### Triggers

__DO NOT USE TRIGGERS__

### Identity fields

Instead of using a trigger to increment a sequence, if the database supports it use an idenity filed:

```sql
 CREATE TABLE TAX_CODES (
    TAX_CODE_ID NUMBER GENERATED BY DEFAULT ON NULL AS IDENTITY,
    ...
);
```

### Functions and procedures

Speficy functions and procedures appropriately. Do not just make everything a procedure.

### Single responsibility principal

The [Single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) - each block of code should do one thing, and one thing well.

### `CASE` expressions

This makes assigning a varable based of a number of options cleaner. Use this method over if-else statements

__GOOD__:

```sql
dept_desc := CASE deptno
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

## Packages

* Package names should start with `pkw_`
* Single parameter functions and procedures definitions are written on a single line
* Multiple parameters functions and procedure definitions have parameters separate lines
* All parameters must have a declaration specified: `in`, `out`, `nocopy` and these should align vertically 

```sql
CREATE OR REPLACE PACKAGE pkw_cust_sal 
AS 
    PROCEDURE find_sal(c_id IN student.id%type);
    
    PROCEDURE find_student(
        p_id  IN  student.id%TYPE,
        p_dep OUT student.department%TYPE);
   
   FUNCTION get_sal RETURN NUMBER;
   
   FUNCTION get_student(
       p_id  IN student.id%TYPE,
       p_dep IN student.department%TYPE)
       RETURN t_student;
END pkw_cust_sal;
/

CREATE OR REPLACE PACKAGE BODY pkw_cust_sal
AS
    ...

    ----------------------------------------------------------------------------
    FUNCTION get_student (
    	p_id  IN student.id%TYPE,
        p_dep IN student.department%TYPE)
    ----------------------------------------------------------------------------
        RETURN t_student
    AS
        l_stu t_student;
    BEGIN
    	...
    END get_student;

    ...

END pkw_cust_sal;
/
```

## Logging

See [Oracel-Base: Instrumenting Your PL/SQL Code](https://oracle-base.com/articles/misc/instrumenting-your-plsql-code) For reference.

Use the `DSP` package to add logging to your code. __DO NOT USE__ use `DBMS_OUTPUT`.

__GOOD__:

```sql
SELECT COUNT(*) INTO l_count FROM blah;
DSP.line('package.procedure', 'l_count=' || l_count);
```

__BAD__:

```sql
SELECT COUNT(*) INTO l_count FROM blah;
DBMS_OUTPUT.PUT_LINE('package.procedure l_count=' || l_count);
```

Add a logging header and footer to each function or procedure.

```sql
PROCEDURE proc_2 (p_id IN NUMBER) 
AS
BEGIN
    DSP.line('package.proc_2', 'Started - p_id=' || p_id);

    SELECT COUNT(*) INTO l_count FROM blah;

    DSP.line('package.proc_2', 'Finished - p_id=' || p_id);
END proc_2;
```

## Tips

Your text editor can help you. __DO NOT USE__ Notepad, WordPad or Word. Do use:

* Toad
* SqlDeveloper
* Notepad ++
* Sublime Text
* Ultra Edit
* VS Code
* this list is endless

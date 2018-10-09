# PL/SQL Style guide

This guide was originally stolen from the [Kickstarter SQL Style Guide](https://gist.github.com/fredbenenson/7bb92718e19138c20591)

## Principles

* We take a disciplined and practical approach to writing code.
* We regularly check-in code to Bitbucket
* We believe consistency in style is very important.
* We demonstrate intent explicitly in code, via clear structure and comments where needed.

## Rules

### General stuff

* No tabs. 4 spaces per indent.
* No trailing whitespace.
* Always capitalize SQL keywords (e.g., `SELECT` or `AS`)
* Variable names should be underscore separated:

  __GOOD__:
  `SELECT COUNT(*) AS backers_count`

  __BAD__:
  `SELECT COUNT(*) AS backersCount`

* Comments should go near the top of your query, or at least near the closest `SELECT`
* Try to only comment on things that aren't obvious about the query (e.g., why a particular ID is hardcoded, etc.)
* Don't use single letter variable names be as descriptive as possible given the context:

  __GOOD__:
  `SELECT ksr.backings AS backings_with_creators`

  __BAD__:
  `SELECT ksr.backings AS b`

* Variable naming
  ```
  Package Global Variables: g_variable_name
  Local Variables         : l_variable_name
  Types                   : t_type_name
  Cursors                 : c_cursor_name
  Exceptions              : e_exception_name
  Parameters        	: p_parameter_name
  ```

* File naming
  ```
  .pks – Package specification.
  .pkb – Package body.
  .sql – Everything else.
  ```

### Logging
(See [Oracel Base: Instrumenting Your PL/SQL Code](https://oracle-base.com/articles/misc/instrumenting-your-plsql-code) For reference)

Use the `DSP` package to add logging to your code. Do not use `DBMS_OUTPUT`.

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
Add a logging header and footer to each function or prcedure.

```sql
PROCEDURE proc_2 (p_id IN NUMBER) AS
BEGIN
  DSP.line('package.proc_2', 'Started - p_id=' || p_id);

  SELECT COUNT(*) INTO l_count FROM blah;

  DSP.line('package.proc_2', 'Finished - p_id=' || p_id);
END proc_2;
```

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

Long Window functions can be on a single line or split across multiple lines depending on their length:

```sql
ROW_NUMBER() OVER (PARTITION BY deptno ORDER BY sal DESC) AS row_num
```

```sql
SUM(1) OVER (PARTITION BY category_id, year
             ORDER BY pledged DESC
             ROWS UNBOUNDED PRECEDING) AS category_year
```

Order (`ASC`, `DESC`) should always be explicit. All window functions should be aliased.

### `FROM`

Only one table should be in the `FROM`. Never use `FROM`-joins:

__GOOD__:

```sql
SELECT
    projects.name AS project_name,
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
    projects.name AS project_name,
    COUNT(backings.id) AS backings_count
FROM ksr.projects AS projects, ksr.backings AS backings
WHERE
    backings.project_id = projects.id
   ...
```

### `JOIN`

__USE ANSI JOIN SYNTAX!__

Explicitly use `INNER JOIN` not just `JOIN`, making multiple lines of `INNER JOIN`s easier to scan:

__GOOD__:

```sql
SELECT
    projects.name AS project_name,
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
    projects.name AS project_name,
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
    projects.name AS project_name,
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
    projects.name AS project_name,
    COUNT(backings.id) AS backings_count
FROM 
    ksr.projects AS projects
    INNER JOIN ksr.backings AS backings 
    ON projects.id = backings.project_id
...
```

### `WHERE`

Multiple `WHERE` clauses should go on different lines and begin with the SQL operator:

```sql
SELECT
    name,
    goal
FROM ksr.projects AS projects
WHERE
    country = 'US'
    AND deadline >= '2015-01-01'
...
```

### `CASE`

```sql
CASE 
    WHEN category = 'Art'
    THEN backer_id
    ELSE NULL
END
```

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
    ELSE 'Unknown';
END IF;
```

### Packages

* Package names should start with `pkw_`
* Single parameter functions and procedures are written on a single line
* Multiple parameters functions and procedure have parameters separate lines
* All parameters must have a declaration: `in`, `out`, `nocopy`

```sql
CREATE OR REPLACE PACKAGE pkw_cust_sal 
AS 
    PROCEDURE find_sal(c_id IN student.id%type);
    
    PROCEDURE find_student(
        p_id  IN student.id%TYPE,
        p_dep IN student.department%TYPE);
   
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
    IS
        l_stu t_student;
    BEGIN
    	...
    END get_student;

    ...

END pkw_cust_sal;
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
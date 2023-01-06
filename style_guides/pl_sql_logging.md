## Logging

** To be Updated **

See [Oracle-Base: Instrumenting Your PL/SQL Code](https://oracle-base.com/articles/misc/instrumenting-your-plsql-code) For reference.

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
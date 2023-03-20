# Database Packages

## Package Names

The following naming conventions should be used for database packages:

```
pkw_<name>        -- Package
pkw_<name>_test   -- UT/PLSQL Test package
```

## Package Procedures and Functions

Procedures and functions in a package should be named as follows: 

```
pw_<name>              -- Procedure
fw_<name>              -- Function
```

All procedure and function parameters should be named according to the variable naming prefixes in this document and direction explicitly specified. For example:

```sql
PROCEDURE pw_timetable_process_apis (
        p_tt_instance      IN  VARCHAR2 DEFAULT NULL,
        p_error_code       OUT VARCHAR2,
        p_error_type       OUT VARCHAR2,
        p_logging_enabled  IN  BOOLEAN DEFAULT g_DSP_DISABLED);
```

## Cursors

Cursor names should be named as follows:

```
c_get_level_m_marks
```

Where a cursor accepts parameters these must be explictly defined and not reference subprogram variables available to the cursor.  For example:

```sql
    c_get_term(p_date IN DATE)
    IS 
    SELECT stvterm_code
      FROM stvterm
     WHERE TRUNC (p_date) BETWEEN TRUNC (stvterm_start_date)
                              AND TRUNC (stvterm_end_date);
```


## Grants and Synonyms

Grants and synonyms for packages should be declared in standalone scripts and not included in the script used to create the package. 


## File Names

Packages should have filenames in the following format, for example pkw_package1.pkb. 

We do not include a JiRA issue number in filenames for package code because these files may be updated with further changes after the object has been created. This is in contrast to tables where changes are made using new scripts. 

Grants and synonym scripts should be prefixed with a Jira issue number as these would normally only be run once and are tied to a specific item of work. 


```
pkw_<name>.pks                   -- Package specification
pkw_<name>.pkb                   -- Package body
pkw_<name>_test.pks              -- UT/PLSQL Test package specification
pkw_<name>_test.pkb              -- UT/PLSQL Test package body
SSDT-001_pkw_<name>_grants.sql   -- Package grants
SSDT-001_pkw_<name>_synonyms.sql -- Package synonyms 

```

## Variable Scope

Ensure that you are declaring variables at the correct *level* so that they can't be incorrectly used or changed.   

Variables can be declared at the following levels:

* **Declared Package Specification** - available to any code running in the same schema, or that has execute permissions on the package. Unless a variable has been declared as a CONSTANT, it could be changed by code outside of your package.
* **Declared in Package Body** - available to any code within the same package.
* **Declared in Functions / Procedures** - available to any code within that function or procedure. 
* **Declared in Anonymous Block** - available within the block of code and any sub-blocks

## Subprogram Scope

Package functions and procedures should only be added to your package specification if it is appropriate for them to be called from outside the package. 

Any procedures or functions that implement logic that should not be used outside the package should only be declared in your package body. 

**Note:** If a subprogram is not declared in your package specification the implementation in your package body must precede any code that calls it *OR* you need to declare the prototype for that subprogram at the top of your package body. 

For example:


```sql
CREATE OR REPLACE PACKAGE pkw_example_package
AS

    -- Public function
    FUNCTION fw_get_public_value
        RETURN VARCHAR2;
        
END pkw_example_package;
/

CREATE OR REPLACE PACKAGE BODY pkw_example_package
AS
    -- Declare private subprogram with package scope
    FUNCTION fw_get_private_value
        RETURN VARCHAR2;

    -- Public function
    FUNCTION fw_get_public_value
        RETURN VARCHAR2
    IS
    BEGIN
    	-- More would happen here!
        RETURN fw_get_private_value;
    END fw_get_value;
    
    -- Private function, only available inside the package
    FUNCTION fw_get_private_value
        RETURN VARCHAR2
    IS
    BEGIN
        RETURN 'A Private Value';
    END fw_get_private_value;
END pkw_example_package;
/
```

# Files, Variables and Naming Conventions

## Variable Names

**If you are working in an existing procedure or function please follow the format that it already uses.** For new objects follow these naming conventions.

These variable naming prefixes are based on those at [Oracle-Base: Naming Conventions](https://oracle-base.com/articles/misc/naming-conventions).

Variable naming prefixes:

  ```
  Package Global Variables: g_variable_name
  Local Variables         : l_variable_name
  Types                   : t_type_name
  Cursors                 : c_cursor_name
  Exceptions              : e_exception_name
  IN Parameters           : i_parameter_name
  OUT Parameters          : o_parameter_name
  IN/OUT Parameters       : io_parameter_name
  ```

Variable names should be underscore separated:

  __GOOD__:
  `SELECT COUNT(*) AS backers_count`

  __BAD__:
  `SELECT COUNT(*) AS backersCount`


Don't use single letter variable names and try to be as descriptive as possible given the context:

  __GOOD__:
  `SELECT ksr.backings AS backings_with_creators`

  __BAD__:
  `SELECT ksr.backings AS b`


### General Naming Guidelines

1. Never use names with a leading numeric character.
2. Always choose meaningful and specific names.
3. Avoid using abbreviations unless the full name is excessively long.
4. Avoid long abbreviations. Abbreviations should be shorter than 5 characters.
5. Any abbreviations used must be widely known and accepted.
6. Never use keywords as names. A list of keywords may be found in the dictionary view v$reserved_words.
7. Avoid adding redundant or meaningless prefixes and suffixes to identifiers. Example: create table emp_table.
8. Always use the same names for elements with the same meaning.


## Database Package Definition

### Package Names

The following naming conventions should be used for database packages:

```
pkw_<name>        -- Package
pkw_test_<name>   -- UT/PLSQL Test package
```

### Package Functions and Procedures

Procedures and functions in a package should be named as follows: 

```
pw_<name>              -- Procedure
fw_<name>              -- Function
```

All procedure and function parameters should be named according to the variable naming prefixes in this document and direction explicitly specified. For example:

```sql
PROCEDURE pw_timetable_process_apis (
        i_tt_instance      IN  VARCHAR2 DEFAULT NULL,
        o_error_code       OUT VARCHAR2,
        o_error_type       OUT VARCHAR2,
        i_logging_enabled  IN  BOOLEAN DEFAULT g_DSP_DISABLED);
```


### File Names

Packages should have filenames in the following format, for example pkw_package1.sql. We do not include a JiRA issue number in filenames for packages because these files may be updated with further changes after the object has been created. This is in contrast to tables where changes are made using new scripts. 


```
pkw_<name>.pks        -- Package specification
pkw_<name>.pkb        -- Package body
pkw_test_<name>.pks   -- UT/PLSQL Test package specification
pkw_test_<name>.pkb   -- UT/PLSQL Test package body
```

### Variable Scope

Ensure that you are declaring variables at the correct *level* so that they can't be incorrectly used or changed.   

Variables can be declared at the following levels:

* **Declared Package Specification** - available to any code running in the same schema, or that has execute permissions on the package. Unless a variable has been declared as a CONSTANT, it could be changed by code outside of your package.
* **Declared in Package Body** - available to any code within the same package.
* **Declared in Functions / Procedures** - available to any code within that function or procedure. 
* **Declared in Anonymous Block** - available within the block of code and any sub-blocks

### Subprogram Scope

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



## Tables

### Table Names

Tables should be given a meaningful name that describes the purpose of the table. All table names should be plural and where the table name contains more than one word only the last should be plural. 

Table names should be underscore separated. 

Tables that are related to a single project or development should be prefixed with an identifier. 

For example:

```
timetable_programmes
timetable_programme_years
timetable_programme_year_modules
timetable_export_log
```

**When creating new tables ensure that you get the right balance of brevity and meaningfulness**. Don't create tables with unnecessarily long names or names that are so short that it is difficult to understand their purpose. 


### Constraints

Table constraints should be named as followed. 

```
pk_<table>                 -- Primary Key
fk_<table>_<optional_text> -- Foreign Key
```

### Table comments

Table and column comments should be included in the creation script for all new tables. 

```sql
COMMENT ON TABLE schema.table IS 'Overall table description';


COMMENT ON COLUMN schema.table.column1 IS 'Column 1 description';
COMMENT ON COLUMN schema.table.column2 IS 'Column 2 description';
```

### Temporary tables

The name for temporary tables should always link back to the Service Now incident or JiRA issue number that they have been created for, for example *srs_001_swradms_table_backup* . 

Note that table names cannot contain hyphen ( - ) characters and these should be replaced with an underscore. 

**You should always plan and schedule change requests to remove these tables when no longer unnecessary. Your JiRA issue should not be marked as done until all temporary tables have been removed.**

### File Names

Scripts that create or update tables should have filenames in the following format, for example ssdt-001_table.sql

```
ssdt-001_<table_name>.sql        -- Create table script
ssdt-001_alter_<table_name>.sql  -- Alter table script
```

## Triggers

### Trigger Names

Triggers should be given a meaningful name that indicates the triggering event (UPDATE/DELETE/INSERT), timing (BEFORE/AFTER) and type ( COMPOUND TRIGGER? ) in the following format:

```
tw_<COMPOUND>_<timing>_<event>_on_<table> -- <COMPOUND> is optional and event can be duplicated to indicate multiple triggering events
```

Use the following abbreviations for timing:

```
AFT -- After
BEF -- Before
```

Use the following abbreviations for triggering events:

``` 
UPD -- Update
INS -- Insert
DEL -- Delete
```

For example:

```
TW_AFT_UPD_ON_SWBDEGC
TW_BEF_DEL_ON_SWRADMS
TW_AFT_INS_UPD_ON_STVDEPT
TW_COMPOUND_AFT_INS_ON_SCBCRSE
```

### File Names

Triggers should have filenames in the following format, for example *tw_aft_upd_on_swbdegc.sql*. We do not include a Jira issue number in filenames for triggers because these files may be updated with further changes after the object has been created. 

```
<trigger_name>.sql
```


## Other Database Objects 

### General Guidance

When creating a **view**:

* Consider if it is really needed. Will there be multiple consumers of this data that need a consistent view?
* Has the view been written with performance in mind? Do table joins take advantage of indexes and keys to speed up data retrieval? How many rows are returned by the view?

When creating a standalone **function** or **procedure**:

* Is this part of a wider development or library of functionality? Should it have been created inside a database package to help logically group it?
* Have you chosen the appropriate subprogram type? Functions should be used to return or calculate a value, and procedures should be used to perform an action. Don't always default to creating a procedure when a function is more appropriate. 

When creating a **type**:

* Consider where is the most appropriate place to declare this type. Does it need to be declared at the schema level,  or could it be declared inside a package?

### Names

When **defining new database objects** of any other type, the following naming conventions should be used.  

```
pw_<name>              -- Procedure
fw_<name>              -- Function
as_<name>              -- View
t_<name>               -- Type
```

### File Names
  
Object definitions should be saved to files with the same name as the object using the **.sql** extension, e.g. *as_student_card_details.sql*. 

```
pw_<name>.sql              -- Procedure
fw_<name>.sql              -- Function
as_<name>.sql              -- View
t_<name>.sql               -- Type
```

## All other Scripts

### General Guidance

* The overall purpose of a script should be documented in header comments and include any relevant implementation details, e.g. which schema the script should be run in, any permissions that are required.
* Wherever possible, scripts should be repeatable and handle any expected errors. A script may COMMIT changes automatically, or can output text via dbms_output to indicate to the person running it if a COMMIT should be issued. 

### File Names

For all other database code filenames should begin with a JiRA issue number:

```
ssdt-001_<description>.sql  -- All other scripts
```
  
## Grants and Synonyms

**Grants and synonyms for tables and objects should be declared in standalone scripts and not included in the script used to create the object.**

When creating an object you should:

* Consider what access is required and to which users. Do not grant broad access *'just in case'* or copy/paste permissions from other objects without considering if that is appropriate. 
* Consider whether a synonym is required. Does your table or object need to be referenced outside of the schema it has been created in and could references simply include the schema?

## Repository File Directories

All source code repositories should use the following directory structure where files of the relevant types are needed. If you do not have any files of a given type, the directory doesn't need to be created. For example, a repository with no trigger definitions will not need a *triggers* folder. 

Where your development includes updates in a number of schemas outside of BANDEV, indicate the relevant schemas using named subfolders. 

```
repository_root_folder 
  
    changelogs -- if this development uses liquibase for deployment
    packages
    	bandev -- Optional, applies to all database folders
        baninst1 -- Optional, applies to all database folders
    triggers
    functions
    procedures
    grants
    synonyms
    views
    tables
    types
    jobs - database schedules
    scripts - all data update scripts
    tests - all test code, including utplsql packages
```    
   
An example directory structure for a repository called **great_stuff_repo** :

```
great_stuff_repo

  packages
	
    bandev -- Optional subfolder to indicate schema

      pkw_great_stuff_spec.sql
      pkw_great_stuff_body.sql
      
    baninst1 -- Optional subfolder to indicate schema
    
      pkw_great_stuff_more_functionality_spec.sql
      pkw_great_stuff_more_functionality_body.sql

  triggers

      tw_after_update_on_table.sql

  scripts

      ssdt-011_insert_table_records.sql 
      ssdt-001_update_table_records.sql

  tests

     pkw_test_great_stuff_spec.sql
     pkw_test_great_stuff_body.sql
 ```


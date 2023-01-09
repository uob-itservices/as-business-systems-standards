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
pkw_<name>_spec        -- Package specification
pkw_<name>_body        -- Package body
pkw_test_<name>_spec   -- UT/PLSQL Test package specification
pkw_test_<name>_body   -- UT/PLSQL Test package body
```

### Package Functions and Procedures

Procedures and functions in a package should be named as follows: 

```
pw_<name>              -- Procedure
fw_<name>              -- Function
```

All procedure and function parameters should be named according to the variable naming prefixes in this document and direction explicitly specified. For example:

```
PROCEDURE pw_timetable_process_apis (
        i_tt_instance      IN  VARCHAR2 DEFAULT NULL,
        o_error_code       OUT VARCHAR2,
        o_error_type       OUT VARCHAR2,
        i_logging_enabled  IN  BOOLEAN DEFAULT g_DSP_DISABLED);
```


### File Names

Packages should have filenames in the following format, for example pkw_package1.sql. We do not include a Jira issue number in filenames for packages because these files may be updated with further changes after the object has been created. This is in contrast to tables where changes are made using new scripts. 


```
pkw_<name>_spec.sql        -- Package specification
pkw_<name>_body.sql        -- Package body
pkw_test_<name>_spec.sql   -- UT/PLSQL Test package specification
pkw_test_<name>_body.sql   -- UT/PLSQL Test package body
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

The name for temporary tables should always link back to the Service Now incident or JiRA issue number that they have been created for, for example *srs-001_swradms_table_backup* . 

**You should always plan and schedule change requests to remove these tables when no longer unnecessary.**

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

When **defining new database objects** of any other type, the following naming conventions should be used.  

```
pw_<name>              -- Procedure
fw_<name>              -- Function
as_<name>              -- View
t_<name>               -- Type
```
  
Object definitions should be saved to files with the same name as the object using the **.sql** extension, e.g. *pkw_end_of_session_spec.sql*. 


## Data and Table Update Scripts

For all other database code, including new tables, filenames should begin with a JiRA issue number:

```
ssdt-001_<table_name>       -- Database tables
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


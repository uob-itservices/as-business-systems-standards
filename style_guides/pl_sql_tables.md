# Database Tables

## Table Names

Tables should be given a meaningful name that describes the purpose of the table. All table names should be plural and where the table name contains more than one word only the last should be plural. 

Table names should be underscore separated. 

In a shared database schema ( e.g. BANDEV ) , tables that relate to a specific domain should use a prefix to relate them to one another. This is not required in a schema explicitly created for a specific domain ( e.g. TRAVEL_COVER )

Some example table names in a shared database schema: 

```
timetable_programmes
timetable_programme_years
timetable_programme_year_modules
timetable_export_log
```

### General Guidance

**Don't prefix table names with programme or project names.** For  example, StARS or New Core. Always name tables according to domain ( in a shared schema ) and purpose. 

**When creating new tables ensure that you get the right balance of brevity and meaningfulness**. Don't create tables with unnecessarily long names or names that are so short that it is difficult to understand their purpose. 


## Constraints / Indexes

Table constraints should be named as followed. Where an index is manually defined it should follow the convention below. 

```
pk_<table>                 -- Primary Key
fk_<table>_<optional_text> -- Foreign Key
idx_<table>_<description>  -- Manually defined index
```

## Table comments

Table and column comments should be included in the creation script for all new tables. 

```sql
COMMENT ON TABLE schema.table IS 'Overall table description';


COMMENT ON COLUMN schema.table.column1 IS 'Column 1 description';
COMMENT ON COLUMN schema.table.column2 IS 'Column 2 description';
```

## Temporary tables

The name for temporary tables should always link back to the Service Now incident or JiRA issue number that they have been created for, for example *srs_001_swradms_table_backup* . 

Note that table names cannot contain hyphen ( - ) characters and these should be replaced with an underscore. 

**From a development process perspective, a new 'Task' category JiRA issue should be created for all work that includes the creation of a temporary table. This task should be used to track the lifetime of the table and schedule removal when it is no longer needed. **

## File Names

Scripts that create or update tables should have filenames in the following format, for example ssdt-001_table.sql

```
ssdt-001_<table_name>.sql        -- Create table script
ssdt-001_alter_<table_name>.sql  -- Alter table script
```

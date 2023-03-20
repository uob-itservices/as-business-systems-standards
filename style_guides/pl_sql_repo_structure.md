# Repository Directory Structure

All source code repositories should use the following directory structure where files of the relevant types are needed. If you do not have any files of a given type, the directory doesn't need to be created. For example, a repository with no trigger definitions will not need a *triggers* folder. 

Where your development includes updates in a number of schemas outside of a single schema, this can be indicated using top level schema folders. If all of your code resides in a single schema, this is not required. However your deployment scripts and/or TCD must make it clear which schema changes should be applied to. 

```
repository_root_folder 
  
    changelogs -- if this development uses liquibase for deployment
    packages
    triggers
    functions
    procedures
    grants
    synonyms
    views
    tables
    types
    apex - APEX application exports
    jobs - database schedules
    scripts - all data update scripts
    tests - all test code, including utplsql packages
    ords - Anything relating to ords API setup
```    
   
An example directory structure for a repository called **great_stuff_repo** :

```
great_stuff_repo

  packages
	
      pkw_great_stuff.pks
      pkw_great_stuff.pkb
      pkw_great_stuff_more_functionality.pkb
      pkw_great_stuff_more_functionality.pks

  triggers

      tw_after_update_on_table.sql

  scripts

      ssdt-011_insert_table_records.sql 
      ssdt-001_update_table_records.sql

  tests

     pkw_test_great_stuff_spec.sql
     pkw_test_great_stuff_body.sql
 ```


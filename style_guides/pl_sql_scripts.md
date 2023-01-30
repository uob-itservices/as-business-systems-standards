# Scripts

## General Guidance

* The overall purpose of a script should be documented in header comments and include any relevant implementation details, e.g. which schema the script should be run in, any permissions that are required.
* Wherever possible, scripts should be repeatable and handle any expected errors. A script may COMMIT changes automatically, or can output text via dbms_output to indicate to the person running it if a COMMIT should be issued. 

## File Names

For all other database code filenames should begin with a JiRA issue number:

```
ssdt-001_<description>.sql  -- All other scripts
```
  
## Grants and Synonyms

**Grants and synonyms for tables and objects should be declared in standalone scripts and not included in the script used to create the object.**

When creating an object you should:

* Consider what access is required and to which users. Do not grant broad access *'just in case'* or copy/paste permissions from other objects without considering if that is appropriate. 
* Consider whether a synonym is required. Does your table or object need to be referenced outside of the schema it has been created in and could references simply include the schema?
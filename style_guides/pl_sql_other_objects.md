# Other Database Objects 

## General Guidance

When creating a **view**:

* Consider if it is really needed. Will there be multiple consumers of this data that need a consistent view?
* Has the view been written with performance in mind? Do table joins take advantage of indexes and keys to speed up data retrieval? How many rows are returned by the view?

When creating a standalone **function** or **procedure**:

* Is this part of a wider development or library of functionality? Should it have been created inside a database package to help logically group it?
* Have you chosen the appropriate subprogram type? Functions should be used to return or calculate a value, and procedures should be used to perform an action. Don't always default to creating a procedure when a function is more appropriate. 

When creating a **type** or **record**:

* Consider where is the most appropriate place to declare this type. Does it need to be declared at the schema level,  or could it be declared inside a package?

## Names

When **defining new database objects** of any other type, the following naming conventions should be used.  

```
pw_<name>    : Procedure
fw_<name>    : Function
as_<name>    : View
t_<name>     : Type
t_rec_<name> : Record Type
```

## Grants and Synonyms

Grants and synonyms for objects should be declared in standalone scripts and not included in the script used to create the object.

## File Names
  
Object definitions should be saved to files with the same name as the object using the **.sql** extension, e.g. *as_student_card_details.sql*. 

Grants and synonym scripts should be prefixed with a Jira issue number as these would normally only be run once and are tied to a specific item of work. 

```
pw_<name>.sql                   --  Procedure
fw_<name>.sql                   -- Function
as_<name>.sql                   -- View
t_<name>.sql                    -- Type
SSDT-001_pw_<name>_grants.sql   -- Package grants
SSDT-001_fw_<name>_synonyms.sql -- Package synonyms
```

## APEX Application File Names

Application files exported from APEX should use the folliwing naming pattern. As with other object scripts APEX application files SHOULD NOT include the Jira issue number they were initially introduced as part of and the same file should be updated in source control each time that the application is altered. 

>[!IMPORTANT]
>Your application must retain the same application ID in all environments and it is important that new applications DO NOT reuse an existing application ID

```
[application_id]_[descriptive_name].sql
```



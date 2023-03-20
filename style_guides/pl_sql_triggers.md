# Database Triggers

## Trigger Names

Triggers should be given a meaningful name that indicates the triggering event (UPDATE/DELETE/INSERT), timing (BEFORE/AFTER) and type ( COMPOUND TRIGGER? ) in the following format:

```
tw_<COMPOUND>_<timing>_<event>_on_<table> -- <COMPOUND> is optional and event can be duplicated to indicate multiple triggering events
```

Use the following abbreviations for timing:

```
AFTER -- After
BEFORE -- Before
```

Use the following abbreviations for triggering events:

``` 
UPDATE -- Update
INSERT -- Insert
DELETE -- Delete
```

For example:

```
TW_AFTER_UPDATE_SWBDEGC
TW_BEFORE_DELETE_SWRADMS
TW_AFTER_INSERT_UPDATE_STVDEPT
```

## File Names

Triggers should have filenames in the following format, for example *tw_after_update_on_swbdegc.sql*. We do not include a Jira issue number in filenames for triggers because these files may be updated with further changes after the object has been created. 

```
<trigger_name>.sql
```

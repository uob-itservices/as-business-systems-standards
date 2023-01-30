# Database Triggers

## Trigger Names

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

## File Names

Triggers should have filenames in the following format, for example *tw_aft_upd_on_swbdegc.sql*. We do not include a Jira issue number in filenames for triggers because these files may be updated with further changes after the object has been created. 

```
<trigger_name>.sql
```

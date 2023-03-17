# Comments and Change History

When creating database objects or scripts you should always include a header comment and history. For packages, you should include this in both the package spec and body to track changes to each. 

The header should contain the following information:

* Description - a summary of the package and an enhancement number
* List of authors
* Link to source code repository

We do not create or include version numbers for items in the change history. 

Each time the object is changed a new line should be added to the header. Each history line should contain:

* Date (YYYY-MM-DD)
* JIra Issue ID ( e.g. SSDT-333 )
* Author name
* Summary comment

```sql
CREATE OR REPLACE PACKAGE pkw_great_stuff 
AS
   /**
     * Provides data for some module
     * Created as part of SSDT-001
     *
     * @author Joe Bloggs <email1@bham.ac.uk>
     * @author Fred Perry <email2@bham.ac.uk>
     *
     * @see https://bitbucket.org/great_repo
     */ 
     
   /**
     * History
     * 
     * 2019-01-15     SSDT-001    Joe Bloggs      Package created
     */
    
    ...
    
END pkw_great_stuff;
```

## Inline Comments

Inside a database object or script only use the line commenting technique ```--``` . 


## Commented code

Delete commented code. If you are worried about losing it, it should be stored in version control against a specific commit. Leaving commented out code in your source contributes toward code rot. 

See [What Is Code Rot and How Do You Identify It?](https://linearb.io/blog/what-is-code-rot/) (*external link*) for more information. 
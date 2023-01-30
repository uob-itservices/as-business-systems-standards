# PL/SQL Style guide

This guide uses parts of and expands upon  the [Kickstarter SQL Style Guide](https://gist.github.com/fredbenenson/7bb92718e19138c20591) and the [Trivadis PL/SQL and & SQL Coding Guidelines](https://trivadis.github.io/plsql-and-sql-coding-guidelines/v4.2/).

## Principles

* We take a disciplined and practical approach to writing code.
* We regularly check-in code to source control.
* We believe consistency in style is very important.
* We demonstrate intent explicitly in code, via a clear and consistent structure, using comments where needed.
* We are pragmatic and do not change legacy code for change's sake.

It is split into the following sections:

1. [Files, Variables and Naming Conventions](pl_sql_naming_conventions.md)
2. [Comments and Change History](pl_sql_comments_history.md)
3. [Variables Naming Conventions](pl_sql_naming_conventions.md)
4. [Tables](pl_sql_tables.md)
5. [Packages](pl_sql_packages.md)
6. [Triggers](pl_sql_triggers.md)
7. [Other Objects](pl_sql_other_objects.md)
8. [Scripts](pl_sql_scripts.md)
9. [Repository Directory Structure](pl_sql_repo_structure.md) 
11. [Formatting PL/SQL and SQL Code](pl_sql_fomatting_code.md)
12. [General PL/SQL Writing Guidance](pl_sql_general_guidance.md)
13. [Logging](pl_sql_logging.md)

## Working with Legacy Code

We have a large amount of code in our student records database that was created prior to the definition of any standards or that adheres to an earlier version of these standards. 

**There is a judgement call to be made when you are changing older code as to whether it should be updated to meet the standards defined here.** This decision needs to be made in light of 1) the significance and extent of changes that are being made and 2) the amount of testing that will be done on your changes, both within the development team and by end users. 

For example, if you are changing a single condition in a package cursor it would not justify updating the entire package to meet these standards.

**Where changes do not meet this threshold you should work to whatever standard exists in the object you are working with.** 

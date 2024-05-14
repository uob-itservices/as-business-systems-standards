# Formatting PL/SQL and SQL Code

We apply some general standards to PL/SQL and SQL code:

* No tabs and 4 spaces per indent. 

* No trailing whitespace.

* Always capitalize keywords (e.g., `SELECT` or `AS`)

* Variables should be in lowercase and underscore separated, with global constant variables capitalised ( see section on Variables and Naming Conventions ). 

> [!CAUTION]
> The team does not currently have a comprehensive standard approach to formatting PL/SQL code. If you are in any doubt about anything regarding formatting, please raise this with the team.

## Unix Line Endings

**All files must use UNIX line endings.**

SQL Developer can **default to using UNIX line endings** for new files by setting *Window -> Preferences -> Environment -> Line Terminator to Line Feed (Unix/Mac)*. Note that this setting will not retrospectively change line endings for existing files and the formatter tool will not update line endings. 

Notepad++ also includes functionality to quickly switch the line endings used by a file. Open your file in Notepad++ and click on *Edit -> EOL Conversion -> Unix (LF)* to switch to Unix line endings. 

The use of a .gitattributes file in your repository can ensure that line endings are always translated to UNIX format on check in of code. Simply create a new text file called **.gitattributes** in the root directory of your repository and add the following line:

```
* text eol=lf
```
